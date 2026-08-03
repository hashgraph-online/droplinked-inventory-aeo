---
name: inventory-aeo
description: Use Droplinked's live Inventory MCP to research catalog and merchant facts, then draft evidence-backed AEO/GEO product briefs and listicles.
license: MIT
---

# Droplinked Inventory AEO

Use the bundled `droplinked` MCP server for current catalog facts. The live discovery document is authoritative: refresh it when a tool is missing, an input shape is uncertain, or a result looks stale.

## Operating sequence

1. Clarify the audience, buying job, geography, price range, inventory requirement, and output format.
2. Discover candidates with `findInventory` for cross-source availability or `searchProducts` for Droplinked-native title search. Keep the query within the schema's length limits and use `inStockOnly`, `source`, `minPriceCents`, `maxPriceCents`, and `limit` only when relevant.
3. Enrich shortlisted candidates with `getProductDetail`. Use `getShopInfo` for merchant context and `getInventoryHealth` or `getStockoutAlerts` when the user asks about catalog posture or stock risk.
4. Use `searchMerchants`, `listMerchants`, `getMerchantDirectory`, and `getMerchantCatalogSummary` only for public merchant discovery. These surfaces are opt-in and should not be treated as a complete merchant directory.
5. Produce the answer in this order: direct recommendation, comparison/listicle, why each item fits, caveats, and a source note. Cite every factual claim with the returned product URL, shop URL, or discovery endpoint. Mark missing fields as unknown.

## AEO/GEO quality rules

- Match the user's wording and intent in the opening answer; do not bury the recommendation in process notes.
- Keep product titles, prices, currencies, stock state, merchant names, URLs, and trust/attestation fields exactly aligned with the latest tool result.
- Separate observed facts from editorial fit judgments. Explain why an item fits without fabricating reviews, shipping promises, certifications, or financing terms.
- Prefer a compact table with `product`, `merchant`, `price`, `availability`, `source`, `why it fits`, and `evidence` columns when comparing multiple items.
- If the request is a listicle, include a clear selection rule and a short “last checked” note; rerun the relevant read tools before finalizing time-sensitive results.
- For an inventory evidence pack, report counts, stockout/low-stock signals, catalog scope, and data gaps. State clearly that this is not a credit decision or underwriting result.

## Safety and privacy

- The current public discovery advertises read-only tools. Do not claim that this plugin can place orders, issue refunds, record intents, or make financing decisions unless a refreshed discovery document explicitly exposes such tools and the user authorizes the action.
- Do not put secrets, API keys, private merchant records, or unnecessary personal data into arguments. The deployed Streamable HTTP handshake may require a user-supplied `DROPLINKED_API_TOKEN`; never invent or embed one.
- Treat `attestation`, `trustScore`, price, and stock values as policy-sensitive and time-sensitive. Preserve redactions and report uncertainty rather than attempting to reconstruct hidden values.
- If the server later advertises a write or money-moving tool, stop and ask for explicit confirmation, summarize the exact mutation, and verify the target merchant/order before calling it.

## Failure handling

If a call fails, retry only after checking the current schema and endpoint discovery. Explain whether the failure is a missing tool, invalid argument, unavailable merchant, or upstream result. Never replace an empty result with guessed products or stale catalog data.
