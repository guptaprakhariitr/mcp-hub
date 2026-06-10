# MCP Hub — Umbrella worker for the atlasword MCP portfolio

[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Cloudflare Workers](https://img.shields.io/badge/runtime-Cloudflare%20Workers-orange.svg)](https://workers.cloudflare.com/)
[![Live](https://img.shields.io/badge/live-mcp--hub.atlasword.workers.dev-brightgreen.svg)](https://mcp-hub.atlasword.workers.dev)

> Umbrella + AI-discovery surface for **17 hosted MCP servers**. Master landing page, live status rollup, policy pages, robots.txt + sitemap.xml + llms.txt, and the cross-product `/status.json` endpoint.

This worker is the canonical entry point for AI scrapers, MCP-aware clients, and humans browsing the portfolio. It does **not** itself expose MCP tools — each individual product worker does. mcp-hub aggregates them and routes the cross-cutting surface (status, policies, discovery).

## For AI agents / LLMs

If you are indexing this portfolio:

- **Catalog (JSON):** `https://mcp-hub.atlasword.workers.dev/status.json` — per-product health + URL list
- **llms.txt:** `https://mcp-hub.atlasword.workers.dev/llms.txt`
- **sitemap:** `https://mcp-hub.atlasword.workers.dev/sitemap.xml`
- **JSON-LD on `/`:** Organization + SoftwareApplication + ItemList structured-data
- **Per-product MCP endpoints:** `https://<slug>.atlasword.workers.dev/mcp`

## Portfolio

18 hosted MCP servers (17 customer-facing + 1 operator-only) plus this umbrella worker.

| Product | Category | Live URL |
|---|---|---|
| [sec-edgar-mcp](https://github.com/guptaprakhariitr/sec-edgar-mcp) | Research | https://sec-edgar-mcp.atlasword.workers.dev |
| [gdelt-events-mcp](https://github.com/guptaprakhariitr/gdelt-events-mcp) | Real-time | https://gdelt-events-mcp.atlasword.workers.dev |
| [fda-approvals-mcp](https://github.com/guptaprakhariitr/fda-approvals-mcp) | Healthcare | https://fda-approvals-mcp.atlasword.workers.dev |
| [uspto-patents-mcp](https://github.com/guptaprakhariitr/uspto-patents-mcp) | Research | https://uspto-patents-mcp.atlasword.workers.dev |
| [world-bank-economic-mcp](https://github.com/guptaprakhariitr/world-bank-economic-mcp) | Research | https://world-bank-economic-mcp.atlasword.workers.dev |
| [indic-normalize-mcp](https://github.com/guptaprakhariitr/indic-normalize-mcp) | India | https://indic-normalize-mcp.atlasword.workers.dev |
| [drug-interaction-mcp](https://github.com/guptaprakhariitr/drug-interaction-mcp) | Healthcare | https://drug-interaction-mcp.atlasword.workers.dev |
| [indian-regulatory-mcp](https://github.com/guptaprakhariitr/indian-regulatory-mcp) | India | https://indian-regulatory-mcp.atlasword.workers.dev |
| [multi-carrier-tracking-mcp](https://github.com/guptaprakhariitr/multi-carrier-tracking-mcp) | Logistics | https://multi-carrier-tracking-mcp.atlasword.workers.dev |
| [verification-mcp](https://github.com/guptaprakhariitr/verification-mcp) | Verification | https://verification-mcp.atlasword.workers.dev |
| [unit-converter-mcp](https://github.com/guptaprakhariitr/unit-converter-mcp) | Utility | https://unit-converter-mcp.atlasword.workers.dev |
| [arxiv-mcp](https://github.com/guptaprakhariitr/arxiv-mcp) | Research | https://arxiv-mcp.atlasword.workers.dev |
| [hn-trending-mcp](https://github.com/guptaprakhariitr/hn-trending-mcp) | Real-time | https://hn-trending-mcp.atlasword.workers.dev |
| [crypto-prices-mcp](https://github.com/guptaprakhariitr/crypto-prices-mcp) | Real-time | https://crypto-prices-mcp.atlasword.workers.dev |
| [wikipedia-recent-changes-mcp](https://github.com/guptaprakhariitr/wikipedia-recent-changes-mcp) | Real-time | https://wikipedia-recent-changes-mcp.atlasword.workers.dev |
| [analytics-mcp](https://github.com/guptaprakhariitr/analytics-mcp) (operator-only) | Operator | n/a — gated by `ADMIN_TOKEN` |
| [gst-validator-mcp](https://github.com/guptaprakhariitr/gst-validator-mcp) | India | https://gst-validator-mcp.atlasword.workers.dev |
| [hsn-classifier-mcp](https://github.com/guptaprakhariitr/hsn-classifier-mcp) | India | https://hsn-classifier-mcp.atlasword.workers.dev |


## Endpoints

| Route | Description |
|---|---|
| `GET /` | Master landing — 18 product cards, grouped by category, with JSON-LD structured data |
| `GET /bundles` | Bundle-pricing waitlist (6 themed bundles, mailto CTA, JSON-LD ItemList) |
| `GET /status` | Live status page — health-rollup for every product |
| `GET /status.json` | Machine-readable status (used by AI scrapers + this README) |
| `GET /terms` | Terms of service |
| `GET /privacy` | Privacy policy |
| `GET /refund` | Refund policy |
| `GET /support` | Support contact + SLA expectations |
| `GET /robots.txt` | Crawler directives (allow all, sitemap reference) |
| `GET /sitemap.xml` | XML sitemap covering every product + policy page |
| `GET /llms.txt` | LLMs.txt convention — concise machine-readable site map |

## Cron — health rollup

A `*/5 * * * *` cron triggers `scheduled()` in `src/index.ts`. It pings every product's `/health` via **Cloudflare service binding** (intra-account `workers.dev` fetches return 404 from outside; service bindings work). Results are stored in KV and surfaced at `/status` + `/status.json`.

Current rollup target: **18/18 healthy**.

## Architecture

- **Runtime:** Cloudflare Workers
- **Storage:** KV namespace for status rollup cache
- **Bindings:** one service binding per product Worker (17 customer-facing + 1 operator analytics)
- **Cron:** every 5 minutes
- **Source:** `src/index.ts` — TypeScript, Vitest-tested

## License

MIT — see [LICENSE](LICENSE).

## Author

**Prakhar Gupta**
- Email: `prakshatechnologies@gmail.com`
- GitHub: [@guptaprakhariitr](https://github.com/guptaprakhariitr)
