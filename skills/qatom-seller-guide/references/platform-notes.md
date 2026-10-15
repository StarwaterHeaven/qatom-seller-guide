# Qatom platform notes (observed; confirm against the dashboard)

Last reviewed: 14 October 2026. Qatom ships often; anything marked "observed" can change.

## Connect

- **Master MCP:** `https://mcp.m.todaq.net` (its canonical address since 15 Oct 2026; `/mcp` still works for connectors saved with it). OAuth via `https://pay.m.todaq.net`, scopes `openid profile email twin`. Add it as a custom connector in your client (Claude: Settings > Connectors > Add custom connector). Grok and other MCP clients take the same URL.
- Connecting the master MCP gives discovery of every public item on the catalog. That is the one connection a buyer needs.
- Each seller also gets their own MCP instance; private and featured items behave differently there (below).
- API docs: https://docs.m.todaq.net. OpenAPI: `https://pay.m.todaq.net/v4/openapi.json`.

## Dashboard (dashboard.qatom.ai) — walked 11 Oct 2026

- **Overview:** account name and plan, MCP status, tiles for MCP instances, catalog items and revenue.
- **Catalog:** + New item; search; filters (Not archived, Active, Inactive, Archived, All); sort. Item page: Duplicate, Details, Agents (Featured), Pricing (price, receipt line item), Service (method, endpoint, schema editor that validates), Save changes, Request headers & secret parameters (own save), Availability (Deactivate, Archive), Sales, Commodity ID, Visibility, Record.
- **New item:** Name, Descriptor (the receipt and card-statement line), Description, Digital quantity (USD TDN), Price, Method (defaults to GET), Endpoint, Service schema, Active, **Private to this MCP instance**, Featured, Request headers & secret parameters, Create item.
- **MCP:** server URL, private flag, deploy version, health, tools (featured and built-in, with schemas), logs, **custom domain** (subdomain by CNAME), **Reboot instance**, **Destroy instance** (irreversible: new hostname and key).
- **Twins:** every twin of the account (primary, agent, provider): hostname, display name, **API key** (used for Payment Multiplex payers and direct twin calls), balances.
- **Settings:** **Billing** (plans: **Builder** free, **Pro** $20/mo, **Max** $300/mo, Enterprise; card; invoices), **API Keys** (create, revoke), **Secrets** (client credentials; the public secret used by embeds and guest checkout).
- **Account:** business name (the seller name buyers see), address, tax number, billing email (verify), delete account.
- Observed 11 Oct 2026: item pages can be slow to load; wait until the form shows the item you clicked before saving.

## Wallets (twins)

- Signing in provisions a **primary twin** (the owner's wallet, MFA-gated) and an **agent twin** (the agent's wallet, used by `agent_checkout`).
- `agent_wallet_info` shows the agent twin and its balances. `fund_primary_wallet` returns the page where the human adds funds by card. `transfer_to_agent` moves funds from primary to agent twin; the human starts it.
- The agent twin's balance is the agent's hard spending ceiling.
- Observed: balance reads can lag a purchase by a few minutes; re-read before concluding anything.

## Buy

- `catalog_search` (optional `query`, `limit`, `offset`, `created_after`) returns id, name, description, price, seller and input schema for each item.
- `agent_checkout` with `catalog_item_id` and `arguments` buys from the agent twin immediately and returns the seller's response inside the receipt, with a transaction id.
- `guest_checkout` returns a checkout link the human completes by card. Observed: card payments have a minimum of about $0.50, so sub-50-cent items suit agent wallets, not guest card checkout.
- Free (price 0) items charge 0.000 and return their result in the receipt.
- **Fulfilment responses are not retrievable later** (observed 14 Oct 2026): there is no read-only call to fetch a purchase's delivery again. A buyer agent that truncated the response (piping it through `tail`) lost the stream and download links, then re-bought to recover them and was charged twice more. Save the full response before parsing; never re-buy to recover a link. Reported to Qatom as a feature request (a read-only delivery re-fetch by transaction id).
- **Carts** (live 12 Oct 2026, master MCP): `cart_add_item` (omit `cart_id` to start one; `catalog_item_id`, `arguments`, `quantity` up to 20; each copy is a separate purchase), `cart_view`, `cart_update_item` (quantity, 0 removes, or new arguments), `cart_remove_item`, `cart_checkout` (one card link for the whole cart; nothing charged until the human pays; the cart stays editable until paying starts and the link shows the change), `cart_pay` (pays the whole cart from the agent wallet: within the auto-approve limit it pays at once; above it the first call returns an approval link and `approval_id`, then call again with both), `cart_finish` (stop a part-paid cart, keep what was bought), `cart_discard` (refused once paying starts at the link).
- Observed 12 Oct 2026, a 3-item cart (0, 0.005, 0.01) paid by `cart_pay` within a 1 USD-TDN limit: no approval, each line delivered with its own transaction id and merchant answer in that one result (it is not returned again). Line ids continue across carts, so read `cart_view` before changing a line. One receipt email lists every item; it shows the cart id, not the per-item transaction ids, and its balance line was read before the last item was charged (reported).

## Sell

- Create items in the dashboard: name, description, price (USD-TDN per call), method (GET or POST), endpoint URL, input schema, visibility, featured.
- **Request headers & secret parameters:** observed 9 Oct 2026. Per item, the seller adds headers, query parameters and URL values that Qatom sends on every fulfilment call. Sensitive values are stored encrypted and never shown again; Not sensitive values stay visible in the dashboard; buyers see neither. The endpoint field takes `{input.name}` (a required schema input) and `{secret.name}` (a saved URL value). The section has its own Save headers & parameters button. The dashboard notes that the endpoint URL itself is visible to other users of the account.
- **Receipt line item:** observed 9 Oct 2026, shown on the checkout confirmation, the receipt and the card statement; renaming the item does not change it.
- **Availability:** Deactivate removes an item from search and refuses new purchases on the next call (reversible); Archive takes a mislisted or retired item off sale and out of the catalog list.
- **Arguments:** only properties declared in the schema are forwarded; undeclared inputs are rejected with a clear message. For POST items the arguments arrive as a JSON body.
- **Free items:** a price of 0 bypasses payment and forwards the call.
- **Private:** hidden from the master catalog search, visible on the seller's own MCP instance.
- **Featured:** shown as named tools on the seller's own MCP instance.
- **Edits:** observed 24 Sep 2026, price and schema edits propagate to the master and instances. Items edited before that release may need a re-edit.
- **Description length:** the guide's limit is 1,024 characters (raised from 500 on 8 Oct 2026). Live listings of about 1,000 characters were observed in October 2026.
- **Price precision:** USD-TDN settles to 0.001. Observed Sept 2026, the dashboard accepts prices with more decimals than that; don't use them.
- **Seller name:** set in the dashboard. Observed Oct 2026, items from sellers without one show the seller as "None" in search.
- **Purchase approval:** an agent's purchase asks the human to approve unless its price is at or under the human's auto-approve limit. Read it with `GET /v4/user/agent-approvals/settings`, set it with `PUT` (`auto_approve_limit`; `0` = always ask). Only the human's own credentials can change it (OpenAPI, 10 Oct 2026).
- **Sale verification:** each fulfilment call carries `X-Qatom-Transaction-Id`; `GET /v4/catalog/purchases/{transaction_id}` confirms it is a settled sale of your item. `GET /v4/catalog/revenue` totals what your items have earned (OpenAPI, 10 Oct 2026).
- **Fulfilment parameters by API:** `GET`/`PUT /v4/catalog/items/{id}/dispatch-params` read and replace an item's Request headers & secret parameters.
- **Failed calls:** observed Sept 2026, a call the endpoint rejects can still be charged, and Qatom may relay the failure as a generic "Invalid payment request". Automatic refunds and error pass-through are on the roadmap. Test endpoints directly first, and document error causes in `llms.txt`.
- **Commodity hash:** each item has one (dashboard, or `GET /v4/catalog/items/{id}`). It identifies the item's payments; you need it to find who paid.
- **Public catalog API:** `GET https://pay.m.todaq.net/v4/catalog/items?limit=100&offset=0` lists public items (fields include `commodity_name`, `commodity_description`, `commodity_cost`, `service_method`, `service_schema`, `active`). Use it to generate `llms.txt` from the live catalog.

## Clients (observed 14 Oct 2026)

- The same master MCP URL works in Claude (Customize → Connectors → Add custom connector; leave the detected "Sign in now" and "Register automatically" settings), Grok (grok.com/connectors → Custom) and Muse (ask in chat to connect the URL; Muse shows it as a "Todaq" connector card behind Meta's custom-connector notice). A Muse connection failed after Qatom sign-in on 14 Oct; reported.
- First sign-in: email code (or passkey), then "Sign in faster" (set up a passkey), then "Add funds to your wallet" (5–50 dollars, Apple Pay or card; "Load funds to agentic wallet" is ticked, so it lands in the agent wallet) or Skip for now.
- Claude asks per tool the first time: Deny, Always allow or Allow once.
- MCP Apps (in-chat views) render in Claude, ChatGPT and Hermes; not in Grok or Muse.
- Support address for buyers and sellers: support@todaq.net (Hassan, 12 Oct 2026). The cart receipt footer still says support@m.todaq.net (reported).

## Account tools (from chat)

- Observed 12 Oct 2026 on a Claude connection: `account_choose`, `account_info`, `account_wallets`, `account_catalog`, `account_sales`, `account_mcp_server`, `account_dashboard_link`, `account_create_item`, `account_update_item` (now with a `private` field; behaviour not yet tested), `account_update_details`, `account_rename_wallet`, `account_reboot_server`, `account_finish_change`. They answer "no TODAQ business account chosen yet" until the human picks one at the `account_choose` link. Every change asks for approval and finishes with `account_finish_change`. A Grok connection to the same master MCP, signed in with an email that owns no seller account, listed only the 14 buyer and cart tools: the account tools appear to follow the login (to be confirmed by Qatom). Live test 12 Oct 2026 on Dobox: `account_choose` link → sign in (passkey or email code) → "Which account may your agent use?" (names the asking app, lists the login's seller accounts) → "Done. The agent now uses Dobox." Then `account_info`, `account_wallets`, `account_catalog`, `account_sales`, `account_mcp_server` all answered. `account_rename_wallet` returned an approval link; the approval page (sign in, then Change + Account, Approve) said "Approved. Go back to your conversation to let the agent finish"; `account_finish_change` made the change, and a second call was refused ("cannot make the change twice").
- Sign-in: every approval and choose page asks the human to sign in; a passkey makes it one tap. Recommend setting one up first.

## Pay out (advanced)

- `POST /v4/transfer` with `address` (a twin URL or an email) and `amount` in USD-TDN sends from the caller's **primary** twin. Payments to your items land on the items' twins, so move funds to the primary (manually or with auto-distribution) before paying out.
- Find the payer of an entry with `GET /v4/commodity/{hash}/transactions`.
- One provider twin per thing you sell: `POST /v4/user/accounts/{id}/twins` (label it after the product). `GET /v4/twin/{hostname}/binder` (JSON, CSV or raw) and `/inventory` (CSV) export what a twin holds and how it got there.
- Running the store day to day (distributions, verification, twin paywalls, shared buyer wallets) is in the Console skill: https://github.com/StarwaterHeaven/qatom-console-skill
- Server-side calls need the account's **client credentials** (Client ID and Client secret), from Dashboard → Settings → Secrets; exchange them for an account token at `/v4/account/oauth/token`. Keep the secret on your server only.

## Security habits

- Paid route checks a sensitive `Authorization` header set under Request headers & secret parameters; a secret path adds depth; 404 everywhere else; never echo the URL or headers; rotate on exposure.
- No secrets in the endpoint URL; other dashboard users can see it.
- Optional: accept paid calls only from Qatom's egress addresses (ask the Qatom team for the list).
- Keys in the host's secret store (`wrangler secret put`), never in the repo.

## Updates from TODAQmicro/qatom-skill (15 Oct 2026)

- The official agent guide (`CLAUDE.md`, merged 15 Oct 2026) names the server's canonical URL as its origin and lists the live tools: `catalog_search`, `agent_checkout`, `guest_checkout`, `agent_wallet_info`, `transfer_to_agent`, `fund_primary_wallet`, the read-only `account_*` tools and the `account_*` change tools, each change approved at a link and finished with `account_finish_change`.
- Its rules for agents, adopted in this guide: approvals are never optional and never given by the agent; links come from tool results, never hard-coded; secrets are set up in the dashboard only; `transfer_to_agent` only for an amount the user asked for; agents don't make real-money or live-account changes as a test.
- `agent_checkout` returns an approval link and an `approval_id` unless the purchase is within the user's auto-approve limit; after the user approves, call it again with the same item, the same arguments and the `approval_id`.
- `guest_checkout`: collect every required input in chat first; the card checkout page asks for nothing.
- A seller's own MCP server has the buyer tools and `mcp_server_info` (no `account_*` tools); featured items appear there as their own tools.
- Purchases and top-ups can also be approved in the Qatom mobile app.
- Sign-in is an emailed one-time code or a passkey (the old "2FA code from the app" wording is out of date).
- `account_create_item` fields: `name`, `price`, `endpoint` (required), `description`, `method` (default GET), `inputs`, `active` (default true), `private`, `featured`. No `descriptor`: set it with `account_update_item`, which also takes `archived`.

