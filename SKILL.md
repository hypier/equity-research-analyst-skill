---
name: equity-research-analyst
description: Produce institutional-quality equity research deliverables including initiation reports, earnings analyses and previews, catalyst calendars, morning notes, sector overviews, investment thesis trackers, financial model updates, and stock screens. Use whenever the user asks for equity research, stock analysis, earnings write-ups, coverage initiation, catalyst tracking, sector reports, thesis reviews, financial modeling, or idea generation. Covers US-listed equities, ETFs, and international tickers resolvable via TradingView. Prefers structured TradingView-backed data over unstructured Web Search.
---

# Equity Research Analyst

Produce institutional-grade equity research deliverables covering nine distinct workflows. Each workflow has its own dedicated reference file in `references/workflows/`; this SKILL.md is the dispatcher and the hub for cross-workflow conventions.

## When to invoke which workflow

Match the user request to one of the nine workflows below and read the corresponding reference file from `references/workflows/`. If the request is ambiguous, ask the user to confirm before committing.

| Workflow | Trigger phrases | Reference |
|---|---|---|
| **Initiating coverage** | "initiation report", "first-time coverage", "new coverage on X", "write a full report on X" | `references/workflows/initiating-coverage.md` |
| **Earnings analysis** | "earnings update", "Q1/Q2/Q3/Q4 results for X", "post-earnings report", "beat/miss analysis" | `references/workflows/earnings-analysis.md` |
| **Earnings preview** | "earnings preview", "what to watch in X's earnings", "pre-earnings note" | `references/workflows/earnings-preview.md` |
| **Catalyst calendar** | "catalyst calendar", "upcoming events", "earnings calendar", "event tracker" | `references/workflows/catalyst-calendar.md` |
| **Morning note** | "morning note", "daily brief", "pre-market note", "morning wrap" | `references/workflows/morning-note.md` |
| **Sector overview** | "sector report", "industry overview", "peer comp", "sector deep-dive" | `references/workflows/sector-overview.md` |
| **Thesis tracker** | "thesis tracker", "investment thesis review", "revisit thesis on X", "thesis check" | `references/workflows/thesis-tracker.md` |
| **Model update** | "update the model", "model maintenance", "refresh estimates", "re-forecast" | `references/workflows/model-update.md` |
| **Idea generation** | "screen for X", "find me stocks that", "idea generation", "investment ideas" | `references/workflows/idea-generation.md` |

### Single-workflow discipline (especially for `initiating-coverage`)

The initiation-report workflow has five sequential tasks (Company Research → Financial Modeling → Valuation → Charts → Assembly). Execute **one task per user request**, verify prerequisites before the next task, and never auto-chain. Details in `references/workflows/initiating-coverage.md`.

## Primary data source: `tradings-api`

Before Web Search, pull structured numeric data (financials, TTM ratios, analyst consensus, calendars, prices, technicals, news) from the bundled `tradings-api` (TradingView proxy). **One call to `/api/market-data/{symbol}` covers ~70% of the numeric content of a typical research report.**

- Full endpoint map, curl examples, and JSON-path-to-report-field mapping: `references/tradings-api.md`
- OpenAPI spec and example responses: `references/tradings-api-docs/`

**Authentication**: users must provide their own `RAPIDAPI_KEY` env var (RapidAPI hosted).

**Use Web Search ONLY for narrative content**: MD&A text, forward guidance wording, earnings call transcripts, segment breakdowns, risk factors, management bios, industry research, FDA/regulatory decisions. Pull raw SEC 10-K/10-Q only when direct quotation or audit is required.

## Global conventions

### Data freshness
- Training data is outdated. Before writing, verify today's date and confirm that the latest fetched data is current (next earnings date, last reported quarter).
- If `data.current.fiscal_period_current` is >90 days old, flag as "last reported" and Web Search for newer disclosures.

### Ticker resolution
- Always use `EXCHANGE:TICKER` format (e.g., `NASDAQ:AAPL`, `NYSE:JPM`).
- If the user supplies only a company name, resolve it via `/api/search/market/{query}?filter=stock` before proceeding.

### Citation standard
Every numeric fact in a deliverable must cite its source:

```
Source: Structured data via tradings-api (TradingView); fetched [YYYY-MM-DD]
        Endpoint: /api/market-data/NASDAQ:AAPL
        Fiscal period: 2026-Q1
```

SEC filings keep separate EDGAR hyperlinks. Analyst consensus cites `analyst-recommendations` endpoint (not "Bloomberg").

### Output formatting
- Default font for Word deliverables: **Times New Roman**.
- Do **not** add emojis to research reports unless the user explicitly requests them.
- Follow the page/structure templates in `assets/initiating-coverage/report-template.md` for initiation reports; other workflows use the template embedded in their own reference file.

### No shortcuts
- Deliver exactly the outputs specified in each workflow reference. Do **not** create extra "completion summaries", "executive summaries", or "quick reference guides".
- Do not fabricate data. Missing field → "N/A". Missing consensus → state "consensus not available".

## Fallback strategy
1. `tradings-api` unavailable → fall back to Web Search + SEC EDGAR per the workflow reference.
2. Ticker unresolved → ask the user for `EXCHANGE:TICKER`.
3. Ambiguous workflow → ask the user which deliverable they want.

## Directory layout

```
equity-research-analyst/
├── SKILL.md                              # This file
├── references/
│   ├── tradings-api.md                   # Endpoint map + report-field mapping
│   ├── tradings-api-docs/                # Bundled API spec + examples (snapshot)
│   │   ├── README.md
│   │   ├── openapi.json
│   │   └── examples/ (12 .md files)
│   ├── workflows/                        # One dispatch target per workflow
│   │   ├── initiating-coverage.md
│   │   ├── earnings-analysis.md
│   │   ├── earnings-preview.md
│   │   ├── catalyst-calendar.md
│   │   ├── morning-note.md
│   │   ├── sector-overview.md
│   │   ├── thesis-tracker.md
│   │   ├── model-update.md
│   │   └── idea-generation.md
│   ├── earnings-analysis/                # Deep-dive references for earnings workflow
│   │   ├── workflow.md
│   │   ├── report-structure.md
│   │   └── best-practices.md
│   └── initiating-coverage/              # Deep-dive references for initiation tasks 1-5
│       ├── task1-company-research.md
│       ├── task2-financial-modeling.md
│       ├── task3-valuation.md
│       ├── task4-chart-generation.md
│       ├── task5-report-assembly.md
│       └── valuation-methodologies.md
├── assets/
│   └── initiating-coverage/              # Templates used in output
│       ├── report-template.md
│       └── quality-checklist.md
```
