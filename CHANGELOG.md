# Changelog

## 1.3 — 10 October 2026

- The starter template now checks Qatom's `Authorization` fulfilment header itself (Worker secret `QATOM_FULFILMENT_TOKEN`, with `QATOM_FULFILMENT_TOKEN_PREVIOUS` for rotation), so following the template meets the §2 guard (§ intro).
- Stage 0 points to the new use-case repo for full setup recipes.
- New "After launch: run the store" hand-over to the Qatom Console skill (payments received, verification, distributions, twin paywalls, shared buyer wallets).
- References and README link qatom-use-cases and qatom-console-skill.

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
