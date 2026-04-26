# tradings-api Reference Bundle

This directory contains bundled API reference material for the `equity-research-analyst` skill. Treat it as the source of truth for exact endpoint behavior, downloaded schemas, and example payload shapes, but not as the workflow guide for user-facing deliverables.

## Recommended Reading Order

1. Read `../tradings-api.md` first for the "research task -> endpoint" mapping.
2. Open `openapi.json` when you need exact parameters, schemas, or response fields.
3. Open `examples/*.md` when you need a concrete request pattern or sample payload.
4. If `../tradings-api.md` disagrees with payload structure shown here, trust this folder and update `../tradings-api.md`.

## Search Tips

- Prefer `rg "GET /api/market-data/{symbol}" references/tradings-api-docs/examples`
- Prefer `rg "GET /api/calendar/earnings" references/tradings-api-docs/examples`
- Prefer `rg "earnings_release_next_date|analyst-recommendations" references/tradings-api-docs/examples`
- Open the smallest matching example file instead of loading the largest example bundle wholesale.

## What Is In This Folder

- `openapi.json`: bundled OpenAPI spec for the TradingView proxy service.
- `examples/`: grouped request examples and sample responses by endpoint family.
- `README.md`: this short navigation note.

The most frequently useful example files for this skill are:

- `examples/10-market-data.md` for company, TTM, quarterly, annual, analyst, and valuation data.
- `examples/08-calendar.md` for earnings, dividend, IPO, and macro calendars.
- `examples/01-price-data.md`, `02-quote-data.md`, and `04-technical-analysis.md` for charting and trading context.
- `examples/06-news.md` and `11-ideas.md` for market news and community idea flows.

## Published API

- API listing: `https://rapidapi.com/hypier/api/tradingview-data1`
- Hosted base URL: `https://tradingview-data1.p.rapidapi.com`

## Authentication

This skill does not include credentials. Provide your own RapidAPI key:

```bash
export RAPIDAPI_KEY="your_key_here"
```

Use that key in request headers when calling the hosted API.

## Persistent Configuration Guidance

When guiding a user to set up `RAPIDAPI_KEY`, recommend a user-level shell environment variable as the default long-term solution:

```bash
echo 'export RAPIDAPI_KEY="your_key_here"' >> ~/.zshrc
source ~/.zshrc
```

Why this is the default recommendation:

- Keeps secrets outside the skill folder and outside the packaged `.skill` artifact
- Makes `RAPIDAPI_KEY` available across terminal sessions
- Matches the plain environment-variable usage shown in this skill's examples

If the user wants project-scoped credentials instead of a global shell setting, suggest a local `.env` or `direnv` workflow only as a secondary option. In that case:

- Keep the real secret out of the skill folder whenever possible
- Do not commit secrets to git
- Do not place a real key in `SKILL.md`, `references/`, or packaged assets

### Agent Guidance

When a user asks how to configure the API key, guide them in this order:

1. Recommend `~/.zshrc` for persistent local setup
2. Offer `.env` or `direnv` only if they explicitly want project-scoped configuration
3. Warn against storing real credentials inside the skill project or distributable `.skill` package

## Maintenance Notes

- This repo no longer includes `sync-tradings-api-docs.sh`.
- If these bundled references become stale, refresh the affected files manually.
- Keep detailed endpoint mapping in `../tradings-api.md`; keep this file short and navigational.
