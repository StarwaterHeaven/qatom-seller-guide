# Changelog

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
