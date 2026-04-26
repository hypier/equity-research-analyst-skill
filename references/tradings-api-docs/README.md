# tradings-api Reference Bundle

This directory contains bundled API reference material for the `equity-research-analyst` skill. Treat it as a local lookup bundle, not as the workflow guide and not as the source of truth for user-facing instructions.

## Recommended Reading Order

1. Read `../tradings-api.md` first for the "research task -> endpoint" mapping.
2. Open `openapi.json` when you need exact parameters, schemas, or response fields.
3. Open `examples/*.md` when you need a concrete request pattern or sample payload.

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

## Maintenance Notes

- This repo no longer includes `sync-tradings-api-docs.sh`.
- If these bundled references become stale, refresh the affected files manually.
- Keep detailed endpoint mapping in `../tradings-api.md`; keep this file short and navigational.
