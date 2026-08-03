# Droplinked Inventory AEO

Droplinked Inventory AEO connects Codex to Droplinked's hosted Inventory MCP. It helps an agent discover public inventory and opted-in merchants, inspect shop and stock context, and turn current catalog facts into answer-engine-optimized (AEO/GEO) comparisons and listicles with product-level evidence.

## What it enables

- Find products across Droplinked's catalog and supported inventory sources.
- Filter for available inventory and enrich candidates with product, shop, and stock-health details.
- Discover merchants that have opted into agentic discovery and summarize their public catalog coverage.
- Produce citation-ready product comparisons, buyer guides, and listicles without inventing price, stock, provenance, or trust claims.
- Assemble a factual inventory evidence pack for a merchant's financing conversation. This plugin does not underwrite, approve, deny, or recommend credit.

## Connection

The plugin uses Droplinked's Streamable HTTP MCP endpoint:

```text
https://apiv3.droplinked.com/mcp
```

The companion [`./.mcp.json`](./.mcp.json) contains the install-time wiring. No credential is embedded. The service's discovery document is the contract and should be checked before relying on tool names or schemas:

```text
https://apiv3.droplinked.com/.well-known/mcp.json
```

Live preflight on 2026-08-03 found that the advertised `/mcp` handshake currently returns `401 missing_token`. If Codex reports that response, set a Droplinked-issued token in `DROPLINKED_API_TOKEN`; this package never supplies or invents one. The public legacy routes below accepted schema discovery and read-only calls during preflight, but they do not implement the full `initialize` handshake, so HOL should confirm the intended auth flow before publication.

The submitted source URL is the Droplinked MCP documentation page (`https://docs.droplinked.com/agentic/mcp-server`); this bundle connects to the hosted service and does not vendor or execute a local server checkout. This repository is temporarily hosted at `https://github.com/hashgraph-online/droplinked-inventory-aeo` pending transfer to Droplinked.

## Live tool surface

At packaging time (2026-08-03), live discovery advertised these ten read-only tools:

`findInventory`, `getInventoryHealth`, `getMerchantCatalogSummary`, `getMerchantDirectory`, `getProductDetail`, `getShopInfo`, `getStockoutAlerts`, `listMerchants`, `searchMerchants`, and `searchProducts`.

The roster and JSON Schemas can change. Refresh the discovery document and use `tools/list` as the source of truth instead of copying stale tool definitions from a page or prompt. The legacy JSON-RPC routes remain available for clients that do not yet support Streamable HTTP:

```text
POST https://apiv3.droplinked.com/mcp/v1/tools/list
POST https://apiv3.droplinked.com/mcp/v1/tools/call
```

## AEO workflow

1. Clarify the buyer, use case, geography, budget, and stock requirements.
2. Search with `findInventory` or `searchProducts`; use `inStockOnly` and source/price filters when the brief requires them.
3. Enrich shortlisted items with `getProductDetail`, `getShopInfo`, and, when a merchant is known, `getInventoryHealth` or `getStockoutAlerts`.
4. For merchant discovery, use `searchMerchants`, `listMerchants`, `getMerchantDirectory`, and `getMerchantCatalogSummary`; only opted-in merchants are visible on those surfaces.
5. Write a direct answer first, then a compact comparison or listicle. Cite each factual product claim to the returned `productUrl`, shop URL, or discovery source, and label unavailable fields as unknown.

The bundled [`inventory-aeo` skill](./skills/inventory-aeo/SKILL.md) encodes this workflow and the read-only/privacy guardrails.

## Safety and privacy

Discovery and legacy read routes are public, while the deployed Streamable HTTP handshake may require a token. Do not send API keys, credentials, private merchant data, or unnecessary personal information in tool arguments. Treat stock, price, attestation, and trust fields as time-sensitive. Do not infer financing eligibility or make a purchase decision from catalog data. If a future discovery document advertises write or money-moving tools, require explicit user confirmation before calling them.

## License

MIT. See [`LICENSE`](./LICENSE).
