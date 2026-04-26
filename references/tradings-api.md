# tradings-api Structured Data Reference

This document lists every `tradings-api` (TradingView proxy service) endpoint and maps its response fields to equity research report sections. Shared by all skills in the `equity-research` plugin. **Prefer the structured API for numeric data**; use Web Search only for narrative content (MD&A, forward guidance, earnings call transcripts, segment breakdowns, risk factors).

- OpenAPI source (bundled): `./tradings-api-docs/openapi.json`
- RapidAPI call examples (bundled, grouped by endpoint): `./tradings-api-docs/examples/`
- Base URL (RapidAPI hosted): `https://tradingview-data1.p.rapidapi.com`

> 📦 **Self-contained note**: `tradings-api-docs/` is a bundled local reference copy of the [tradings-api](https://rapidapi.com/hypier/api/tradingview-data1) docs for this skill. If the contents look stale, refresh the affected files manually from the published API materials.

---

## Authentication

**RapidAPI (recommended for external users):**

```bash
curl -H "x-rapidapi-host: tradingview-data1.p.rapidapi.com" \
     -H "x-rapidapi-key: $RAPIDAPI_KEY" \
     "https://tradingview-data1.p.rapidapi.com/api/market-data/NASDAQ:AAPL"
```

**Self-hosted:**

```bash
curl "https://tradingview-data1.p.rapidapi.com/api/market-data/NASDAQ:AAPL"
```

Environment variables: `RAPIDAPI_KEY`, optional `TRADINGS_API_BASE`.

---

## Symbol Format

All `{symbol}` parameters use `EXCHANGE:TICKER` format:

- Stocks: `NASDAQ:AAPL`, `NYSE:JPM`, `NASDAQ:TSLA`
- ETFs: `NYSE:SPY`, `NASDAQ:QQQ`
- Crypto: `BINANCE:BTCUSDT`
- Indices: `SP:SPX`, `NASDAQ:NDX`
- Forex: `FX:EURUSD`
- Futures: `CME_MINI:ES1!`

If the ticker is ambiguous, resolve it first via `/api/search/market/{query}?filter=stock`.

---

## Scenario A: Quarterly Earnings Analysis (`earnings-analysis`, `earnings-preview`)

### One-shot fetch (highest information density)

```bash
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/market-data/NASDAQ:AAPL"
```

Returns 5 blocks that populate the following report fields:

| Report field | JSON path |
|---|---|
| Company description | `data.company.business_description` |
| CEO | `data.company.ceo` |
| Employee count | `data.company.number_of_employees` |
| Sector / industry | `data.company.sector`, `data.company.industry` |
| Market cap | `data.indicators.market_cap_basic` |
| P/E TTM | `data.indicators.price_earnings` |
| 52-week high / low | `data.indicators.price_52_week_high`, `price_52_week_low` |
| Beta (1y / 3y / 5y) | `data.indicators.beta_1_year`, `beta_3_year`, `beta_5_year` |
| Latest earnings date | `data.indicators.earnings_release_date` (Unix seconds) |
| Next earnings date | `data.indicators.earnings_release_next_date` |
| **Past 8 earnings-release timestamps** | `data.indicators.earnings_release_date_h[]` |
| TTM revenue | `data.ttm.total_revenue_ttm` |
| **Past 8 quarters TTM revenue (for charts)** | `data.ttm.total_revenue_ttm_h[]` |
| TTM net income | `data.ttm.net_income_ttm` |
| **Past 8 quarters net income** | `data.ttm.net_income_ttm_h[]` |
| TTM EBITDA | `data.ttm.ebitda_ttm` |
| **Past 8 quarters EBITDA** | `data.ttm.ebitda_ttm_h[]` |
| TTM gross profit | `data.ttm.gross_profit_ttm`, `gross_profit_ttm_h[]` |
| TTM free cash flow | `data.ttm.free_cash_flow_ttm`, `free_cash_flow_ttm_h[]` |
| TTM diluted EPS | `data.ttm.earnings_per_share_diluted_ttm`, `earnings_per_share_diluted_ttm_h[]` |
| TTM gross margin | `data.ttm.gross_margin_ttm` |
| TTM operating margin | `data.ttm.operating_margin_ttm` |
| TTM net margin | `data.ttm.net_margin_ttm` |
| TTM EBITDA margin | `data.ttm.ebitda_margin_ttm` |
| TTM ROE | `data.ttm.return_on_common_equity_ttm` |
| TTM ROIC | `data.ttm.return_of_invested_capital_percent_ttm` |
| TTM R&D expense | `data.ttm.research_and_dev_ttm` |
| TTM capex | `data.ttm.capital_expenditures_ttm`, `capital_expenditures_unchanged_ttm_h[]` |
| Current fiscal period | `data.current.fiscal_period_current` (e.g. `"2026-Q1"`) |
| Current P/E, P/B, P/S | `data.current.price_earnings_current`, `price_book_current`, `price_sales_current` |
| Current EV | `data.current.enterprise_value_current` |
| Current EV/EBITDA | `data.current.enterprise_value_ebitda_current` |
| Current dividend yield | `data.current.dividends_yield_current` |
| Current D/E | `data.current.debt_to_equity_current` |
| Current-quarter EPS actual | `data.financials_quarterly.earnings_per_share_fq` |
| **Next-quarter EPS consensus** ⭐ | `data.financials_quarterly.earnings_per_share_forecast_next_fq` |

**Key insight**: The 8-element `*_ttm_h[]` arrays are the direct data source for earnings-analysis-required charts like "quarterly revenue progression", "quarterly EPS progression", and "quarterly margin trends".

### Beat / miss analysis

```bash
# Analyst ratings, price targets, buy/hold/sell distribution
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/market-data/NASDAQ:AAPL/analyst-recommendations"

# Quarterly three-statement data (balance sheet / income / cash flow)
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/market-data/NASDAQ:AAPL/financials-quarterly"
```

### Price chart and technicals

```bash
# Daily 252 bars (~1 year) for stock price chart
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/price/NASDAQ:AAPL?timeframe=D&range=252"

# Real-time quote (pre/post-market, daily change, volume)
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/quote/NASDAQ:AAPL"

# Multi-timeframe technical signals (for the technical section)
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/ta/NASDAQ:AAPL"

# 50+ individual indicators (RSI / MACD / moving averages / pivots)
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/ta/NASDAQ:AAPL/indicators"
```

---

## Scenario B: Initiation Report (`initiating-coverage`)

**Task 1 — Company Research:**

```bash
curl ".../api/market-data/NASDAQ:AAPL/company"          # Company basic info
curl ".../api/market-data/NASDAQ:AAPL/ipo"              # IPO info
curl ".../api/market-data/NASDAQ:AAPL/credit-ratings"   # S&P / Moody's / Fitch ratings
```

**Task 2 — Financial Modeling:**

```bash
curl ".../api/market-data/NASDAQ:AAPL/financials-annual"   # Annual three statements
curl ".../api/market-data/NASDAQ:AAPL/history-annual"      # Multi-year history arrays
curl ".../api/market-data/NASDAQ:AAPL/cash-flow"           # Cash flow breakdown
```

**Task 3 — Valuation:**

```bash
curl ".../api/market-data/NASDAQ:AAPL/enterprise-value"         # EV / EV/EBITDA
curl ".../api/market-data/NASDAQ:AAPL/ttm"                      # Full TTM ratios
curl ".../api/market-data/NASDAQ:AAPL/analyst-recommendations"  # Target-price consensus
curl ".../api/market-data/NASDAQ:AAPL/dividend"                 # Dividend history
```

**Task 4 — Chart Generation:**

Use `/api/price/` plus the `*_h[]` arrays from `/api/market-data/`. No external data needed.

**Sections still requiring Web Search (~10%):**

- Deep business description, product-line detail → company website + 10-K Item 1
- Management bios → LinkedIn + Proxy Statement (DEF 14A)
- Risk factors → 10-K Item 1A
- Segment breakdown (segment revenue) → 10-K / 10-Q footnotes
- Industry research → Gartner / Forrester / IDC

---

## Scenario C: Catalyst Calendar (`catalyst-calendar`)

```bash
# Ranges must be ≤ 40 days, Unix seconds
FROM=$(date -v+0d +%s)
TO=$(date -v+30d +%s)

# Next 30 days of US earnings releases
curl -H "x-rapidapi-key: $RAPIDAPI_KEY" \
  "https://tradingview-data1.p.rapidapi.com/api/calendar/earnings?from=$FROM&to=$TO&market=america"

# Dividend calendar
curl ".../api/calendar/revenue?from=$FROM&to=$TO&market=america"

# IPO calendar
curl ".../api/calendar/ipo?from=$FROM&to=$TO&market=america"

# Macro economic events (CPI, NFP, FOMC)
curl ".../api/calendar/economic?from=$FROM&to=$TO&market=america,china"
```

---

## Scenario D: Morning Note (`morning-note`)

```bash
curl ".../api/news/stock?lang=en&market_country=US"   # US equity news
curl ".../api/news/economic?lang=en"                  # Macro news
curl ".../api/news?symbol=NASDAQ:AAPL"                # Single-stock news
curl ".../api/news/{newsId}"                          # News detail
```

---

## Scenario E: Screening / Idea Generation (`idea-generation`)

```bash
# Top US gainers
curl ".../api/leaderboard/stocks?tab=gainers&market_code=america&count=50"

# Hot investment ideas (TradingView community)
curl ".../api/ideas/hot?lang=en&page=1"

# Community views on a specific stock
curl ".../api/ideas/list/NASDAQ:AAPL?lang=en"
curl ".../api/ideas/NASDAQ:AAPL/minds?lang=en"
```

Full list of `tab` values: `/api/metadata/tabs?type=stocks`. Common picks: `gainers`, `losers`, `large_cap`, `high_dividend`, `most_volatile`, `52wk_high`, `overbought`, `oversold`, `penny_stocks`.

---

## Scenario F: Sector Overview (`sector-overview`)

```bash
# Sector categorization + columnset metadata (which columns are selectable)
curl ".../api/metadata/columnsets"
curl ".../api/metadata/tabs?type=stocks"

# Filter by sector (using leaderboard with columnset=valuation/profitability/etc.)
curl ".../api/leaderboard/stocks?tab=all_stocks&market_code=america&columnset=valuation&count=100"
```

---

## Ticker Resolution (generic preflight)

If the user supplies only a company name (e.g. "Apple"), resolve it to `EXCHANGE:TICKER` first:

```bash
curl ".../api/search/market/Apple?filter=stock"
# Returns: { data: [ { symbol: "NASDAQ:AAPL", description: "Apple Inc.", ... } ] }
```

---

## Full Endpoint Catalog (60 endpoints)

| Category | Endpoints |
|---|---|
| **Market Data (14)** | `/api/market-data/{symbol}` (full), `/company`, `/ipo`, `/current`, `/ttm`, `/indicators`, `/financials-quarterly`, `/financials-annual`, `/history-quarterly`, `/history-annual`, `/dividend`, `/analyst-recommendations`, `/enterprise-value`, `/credit-ratings`, `/cash-flow` |
| **Quote** | `/api/quote/{symbol}`, `/api/quote/batch` (POST) |
| **Price (OHLCV)** | `/api/price/{symbol}`, `/api/price/batch` (POST) |
| **Technical** | `/api/ta/{symbol}`, `/api/ta/{symbol}/indicators` |
| **Calendar** | `/api/calendar/earnings`, `/revenue`, `/ipo`, `/economic` |
| **News (10)** | `/api/news`, `/news/stock`, `/news/crypto`, `/news/forex`, `/news/futures`, `/news/etf`, `/news/bond`, `/news/index`, `/news/economic`, `/news/{newsId}` |
| **Leaderboard** | `/api/leaderboard/stocks`, `/indices`, `/crypto`, `/futures`, `/forex`, `/bonds`, `/corporate-bonds`, `/etfs`, `/data` |
| **Ideas** | `/api/ideas/hot`, `/editors-picks`, `/list/{symbol}`, `/{symbol}/minds`, `/{imageUrl}` |
| **Search** | `/api/search/market/{query}?filter=stock\|etf\|crypto\|forex` |
| **Metadata** | `/api/metadata/markets`, `/exchanges`, `/tabs`, `/columnsets`, `/languages`, `/world-economy/indicators` |
| **World Economy** | `/api/world-economy/indicators/{indicator}` |
| **Realtime** | `/sse/stream` (JWT required), `/api/token/generate`, `/api/mcp/generate` |
| **Health** | `/health` |

---

## Citation Convention (aligned with `earnings-analysis/SKILL.md` lines 50-107)

When citing data obtained from this API in a report:

```
Source: Structured data via tradings-api (TradingView); fetched [YYYY-MM-DD]
        Endpoint: /api/market-data/NASDAQ:AAPL
        Fiscal Period: data.current.fiscal_period_current = "2026-Q1"
```

SEC filings (10-Q / 10-K) must still be cited separately with EDGAR hyperlinks. Cite the `analyst-recommendations` endpoint in place of "Bloomberg consensus" when data comes from this API.

---

## Fallback Strategy

1. **API unavailable** → fall back to the skill's original Web Search flow
2. **Missing field** (company has no such metric) → keep field as "N/A"; do not fabricate
3. **Stale data** (`fiscal_period_current` > 90 days behind current quarter) → mark as "last reported" and Web Search to confirm whether a newer release exists
4. **Ticker resolution fails** → ask the user to specify `EXCHANGE:TICKER` explicitly
