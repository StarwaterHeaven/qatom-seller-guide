# Qatom platform notes (observed; confirm against the dashboard)

Last reviewed: 8 October 2026. Qatom ships often; anything marked "observed" can change.

## Connect

- **Master MCP:** `https://mcp.m.todaq.net/mcp`. OAuth via `https://pay.m.todaq.net`, scopes `openid profile email twin`. Add it as a custom connector in your client (Claude: Settings > Connectors > Add custom connector). Grok and other MCP clients take the same URL.
- Connecting the master MCP gives discovery of every public item on the catalog. That is the one connection a buyer needs.
- Each seller also gets their own MCP instance; private and featured items behave differently there (below).
- API docs: https://docs.m.todaq.net. OpenAPI: `https://pay.m.todaq.net/v4/openapi.json`.

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
- **Purchase approval:** observed Oct 2026, a purchase may ask the human to approve before it settles (for example above a spend threshold). Expect the prompt when testing your own items.
- **Failed calls:** observed Sept 2026, a call the endpoint rejects can still be charged, and Qatom may relay the failure as a generic "Invalid payment request". Automatic refunds and error pass-through are on the roadmap. Test endpoints directly first, and document error causes in `llms.txt`.
- **Commodity hash:** each item has one (dashboard, or `GET /v4/catalog/items/{id}`). It identifies the item's payments; you need it to find who paid.
- **Public catalog API:** `GET https://pay.m.todaq.net/v4/catalog/items?limit=100&offset=0` lists public items (fields include `commodity_name`, `commodity_description`, `commodity_cost`, `service_method`, `service_schema`, `active`). Use it to generate `llms.txt` from the live catalog.

## Pay out (advanced)

- `POST /v4/transfer` with `address` (a twin URL or an email) and `amount` in USD-TDN sends from the caller's **primary** twin. Payments to your items land on the items' twins, so move funds to the primary (manually or with auto-distribution) before paying out.
- Find the payer of an entry with `GET /v4/commodity/{hash}/transactions`.
- Server-side calls need API client credentials for your account (token from `/v4/account/oauth/token`). Ask the Qatom team for them; a dashboard user key is not enough.

## Security habits

- Paid route checks a sensitive `Authorization` header set under Request headers & secret parameters; a secret path adds depth; 404 everywhere else; never echo the URL or headers; rotate on exposure.
- No secrets in the endpoint URL; other dashboard users can see it.
- Optional: accept paid calls only from Qatom's egress addresses (ask the Qatom team for the list).
- Keys in the host's secret store (`wrangler secret put`), never in the repo.
