# Changelog

## 1.15 — 26 October 2026

From the Donate-on-Pack live demo (Glacier Fresh x WaterAid Canada), 24–26 Oct 2026.

- §2: **When your own page starts the checkout** (QR giving, a web form, a kiosk): create your record and reference first, open the checkout with the reference as the item's argument, make the reference a required schema input with a `pattern`, validate it in the endpoint, and record each verified sale once.
- §6: two launch checks: prove the fulfilment header with one real delivered purchase, not only "no header is refused"; and for page-started checkouts, require the page's reference and run one real lowest-price purchase through the page end to end.
- Pattern library: C6, giving by QR (Donate-on-Pack #219–#222).
- Launch check and platform note: Qatom was seen calling the endpoint with an added path segment (`/qatom/dispatch/1`); answer the path and anything under it.
- Platform notes: page-started checkouts, and an observed automatic return of a payment whose delivery failed (purchase 276).

## 1.14 — 19 October 2026

- Buying: agents paying for themselves always use `agent_checkout`; `guest_checkout` is a payment link for a person. Platform notes record the observed mistake (an agent calls `guest_checkout` first, then corrects) and what to do if it happens. The `llms.txt` "How to call" line now says the same, so sellers' read-mes steer buyer agents the right way.
- Version line in SKILL.md corrected (it still said 1.12).

## 1.13 — 18 October 2026

- §2 Sensitive items: keep the secret out of the chat with an in-chat card (MCP App) that encrypts before the model sees anything; Copy and Clear buttons on reveal pages. Reference adds the Secure Secrets Exchange in-chat app.
- §7 In-chat views: views live on your own free MCP server next to the Qatom master MCP until checkout passes views through; declare `connectDomains` and CORS when a view calls your API.

## 1.12 — 18 October 2026

- The catalog ratings are now **Qatom Scorecard**: intro, launch section and README point at `scorecard.agentkepler.tech/item/{id}` (beta; the same item links move to scorecard.qatom.ai), keep item 200 as the free lookup, and link CONTRIBUTING for anyone who thinks a check is unfair.

## 1.11 — 18 October 2026

From the AquaSentinel build at the Water Care AI Hackathon (17 Oct 2026), and Pirate Kepler's 16 Oct request.

- §2: **When an agent relays for people**: incomplete submissions return 200 with `need`, `candidates` and a ready `reply`; never guess an identifier; structured fields with defaults; one handler behind a free webhook and the paid item; a free preview that stores nothing.
- §6: three new launch checks (relayed submissions, demo and event items, live payments in a demo run beforehand), and after launch, check the item's score with free catalog item 200.
- Intro and README: links to the public scores page and the free lookup item 200.
- Platform notes: schema `pattern` is not enforced by Qatom (validate in the endpoint); live purchases in a demo wait for the owner's approval.
- Pattern library: C5, field reports through a messaging agent and sponsorship (AquaSentinel #212–#214).

## 1.10 — 15 October 2026

From setting up Secure Secrets Exchange (catalog items 196 to 199) end to end with the account tools.

- §2: **Wrapping open-source or third-party software**: close the upstream's own routes with an allowlist and check them from outside (the secrets core behind a paid item still served its own free create API until the tunnel was narrowed).
- §2: **Sensitive items**: a `/security` threat model, `security.txt`, published code, and an end-to-end mode (key in the link's `#` fragment, script hashes pinned in CSP). Reference implementation linked.
- §2: **Expected misses are answers, not errors**: return 200 with `ok: false`, because Qatom turns any non-2xx into a paywall error and drops your message.
- Setup: the connection follows the login (reconnect with the store's email); saving a header can activate an item on its own; passkey at registration (none can be added later today), fresh sign-in per approval and the one-time-code limit.
- §6: four new launch checks (wrapped routes, expected misses, sensitive items, polling off Qatom so it doesn't spend the plan's monthly transactions).
- Platform notes: non-2xx relay, Builder plan limits, account tools and sign-in behaviour, header save activating items.

## 1.9 — 15 October 2026

- New section, high in the guide: **When the seller wants you to set up the store.** Who does what, what to check first, agreeing the whole plan before any change, the order of changes (seller name, items created inactive when they need a secret header, the header on the dashboard, then descriptor and activation), the approve-then-finish loop, reading every change back, launch, and what the agent never does.
- Aligned with the official TODAQmicro/qatom-skill agent guide (15 Oct 2026): master MCP canonical URL is `https://mcp.m.todaq.net`; sign-in by email code or passkey; approvals on the approval page or the Qatom mobile app, never by the agent; links only from tool results; the full `account_dashboard_link` page list; `account_create_item` has no descriptor; method can be PUT.
- §6: `account_update_item` accepts `private` (untested; don't rely on it yet).
- §7: refund path adds the Team Kepler feedback item (catalog item 194).
- USD-TDN described as a USD-denominated digital bearer asset.

## 1.8 — 14 October 2026

- §6: never truncate a fulfilment response; save it as the receipt and never re-buy to recover a link (from Torque's live cart test).
- §7: an optional free "resend my delivery" item keyed on the transaction id.
- Platform notes: fulfilment responses can't be fetched again today.

## 1.7 — 14 October 2026

- §7: refunds and billing problems (seller feedback item first, then support@todaq.net; cart items are quoted by their own transaction id).
- §7: where in-chat views (MCP Apps) render: Claude, ChatGPT and Hermes; not Grok or Muse. Always return text and a link too.
- Platform notes: a Clients section (how Claude, Grok and Muse connect; the first-sign-in passkey and top-up screens; per-tool permission prompts; the support address).

## 1.6 — 12 October 2026

- §2: carts. Each item in a cart still reaches the endpoint as its own paid call; put the link and summary first, and give links enough life to outlast the cart reply.
- §6: test a cart of two of your items; new launch check.
- §4: running the account from chat with the account tools (choose, read, approve each change, secrets stay on the Dashboard).
- Platform notes: cart tools and a live cart test; account tools.
- Account tools tested live (choose, read, approve, finish once); they follow a login that owns a seller account. Recommend a passkey first.

## 1.5 — 11 October 2026

- §6: create items with the visibility they should end up with (public for catalog items; test straight away, announce after the launch report). Private only for items that stay on the seller's own instance. Visibility is fixed at creation; Duplicate is the fallback.
- §4: after saving headers, reload and check the header is listed; Save changes alone can leave it unsaved.

## 1.4 — 11 October 2026

- Platform notes: a Dashboard section from a full walk-through (Overview, Catalog, New item, MCP, Twins, Settings, Account), plan names Builder / Pro / Max / Enterprise.
- Client credentials are self-serve: Dashboard → Settings → Secrets (was "ask the Qatom team").
- §4: Dashboard address, Descriptor field, business name as the seller name, check the form shows the right item before saving.

## 1.3 — 10 October 2026

- The starter template now checks Qatom's `Authorization` fulfilment header itself (Worker secret `QATOM_FULFILMENT_TOKEN`, with `QATOM_FULFILMENT_TOKEN_PREVIOUS` for rotation), so following the template meets the §2 guard (§ intro).
- Stage 0 points to the new use-case repo for full setup recipes.
- New "After launch: run the store" hand-over to the Qatom Console skill (payments received, verification, distributions, twin paywalls, shared buyer wallets).
- References and README link qatom-use-cases and qatom-console-skill.
- Closes the open item on the approval threshold: the human's auto-approve limit (`/v4/user/agent-approvals/settings`) (§6, platform notes).
- New optional guard: verify `X-Qatom-Transaction-Id` with `GET /v4/catalog/purchases/{id}` and serve each sale once (§2, §6).
- Platform notes: dispatch-params API, catalog revenue, provider twin per product, binder and inventory export.

## 1.2 — 9 October 2026

- New in the dashboard: **Request headers & secret parameters** on each catalog item. Added "Fulfilment parameters" to §4: why, the three types (header, query parameter, URL value), Sensitive vs Not sensitive, when to use each, a step-by-step with a Worker header check, rotation, and rules for the agent helping the seller (never handle the secret in chat).
- Endpoint field: `{input.name}` and `{secret.name}` placeholders (§4).
- No secrets in the endpoint URL; other dashboard users can see it (§2, §4, §6, platform notes).
- Guarding the paid route now prefers a sensitive `Authorization` header checked by the endpoint; the secret path remains as added depth (§2, §6, protection principles).
- Pinned values can be Not sensitive fulfilment parameters (§2, §3).
- Platform notes: Request headers & secret parameters, Receipt line item, Availability (deactivate vs archive).
- The Hack the Andes trip status example is now the Mexican freight example (`MXF-` references, `examples/mx-freight-trip-status`).

## 1.1 — 8 October 2026

Moved to its own repository as the canonical version (previously `skills/qatom-seller-guide` in qatom-hack-the-andes).

- Description limit raised from 500 to 1,024 characters (§4, §6 checklist, description pattern, platform notes).
- Descriptions: don't list the individual items covered; do name distinct modes or answer types.
- Seller display name required before publishing (§4).
- Price floor: 0.001 USD-TDN, no more than three decimals, or 0 for free (§4).
- Not-advice line required for health, finance, investment and legal items, in the description and every response (§1, §2, §4).
- No free-form SQL, code or query-string inputs (§2, §3).
- Every schema property needs a description, enum-only ones included (§3).
- Schemas describe ID formats instead of giving real example IDs (§3).
- Brand prefix applies to sellers with more than one item (§4).
- Same content at two prices: say what the higher tier adds (§4).
- Keep items private until the launch report passes; delist test items (§6).
- Testers should expect a purchase-approval prompt (§6, platform notes).

Pending: moving the guide's addresses from todaq.net to qatom.ai, once the new addresses are confirmed.

## 1.0 — 7 October 2026

First release, in qatom-hack-the-andes: Nick Mumford's six stages plus stage 0 (first use case), stage 7 (storefront defaults) and the provenance and money-safety preamble.
