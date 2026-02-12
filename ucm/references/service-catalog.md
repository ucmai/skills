# UCM Service Catalog

> For live data, call `GET https://registry.ucm.ai/v1/services`

## Overview

| Service | Price | Endpoints | Use Case |
|---------|-------|-----------|----------|
| `ucm/web-search` | $0.01 | 1 | Real-time web search |
| `ucm/web-scrape` | $0.02 | 1 | Extract webpage content as markdown |
| `ucm/image-generation` | $0.05 | 1 | Generate images from text prompts |
| `ucm/code-sandbox` | $0.03 | 1 | Execute Python/JS/Bash/R/Java in sandbox |
| `ucm/text-to-speech` | $0.01 | 1 | Convert text to spoken audio |
| `ucm/speech-to-text` | $0.01 | 1 | Transcribe audio to text |
| `ucm/email` | $0.01 | 4 | Send emails (verification required) |
| `ucm/doc-convert` | $0.02 | 1 | Convert PDF/DOCX/XLSX to markdown |
| `ucm/us-stock` | $0.01 | 11 | US stock quotes, financials, news |
| `ucm/cn-finance` | $0.01 | 26 | China A-share data, macro indicators |
| `ucm/weather` | FREE | 4 | Weather, forecast, air quality |

**Total: 11 services, 52 endpoints**

---

## ucm/web-search — $0.01/call

Real-time web search powered by Tavily.

**Endpoint: `search`**
```json
{ "query": "latest AI news", "limit": 10 }
```
- `query` (required): Search query
- `limit` (optional): Max results, default 10

---

## ucm/web-scrape — $0.02/call

Extract content from any webpage as clean markdown or HTML.

**Endpoint: `scrape`**
```json
{ "url": "https://example.com", "format": "markdown" }
```
- `url` (required): URL to scrape
- `format` (optional): `"markdown"` or `"html"`, default `"markdown"`

---

## ucm/image-generation — $0.05/call

Generate images from text prompts using FLUX.1 models.

**Endpoint: `generate`**
```json
{ "prompt": "a sunset over mountains", "width": 1024, "height": 1024 }
```
- `prompt` (required): Text description of the image
- `model` (optional): default `"black-forest-labs/FLUX.1-schnell"`
- `width` / `height` (optional): default 1024
- `n` (optional): number of images, default 1
- `steps` (optional): inference steps, default 4

---

## ucm/code-sandbox — $0.03/call

Execute code in an isolated sandbox. Fresh environment per execution.

**Endpoint: `execute`**
```json
{ "code": "print('hello world')", "language": "python" }
```
- `code` (required): Source code to execute
- `language` (optional): `"python"` | `"javascript"` | `"bash"` | `"r"` | `"java"`, default `"python"`
- `timeout` (optional): max milliseconds, default 30000, max 60000

Returns: `stdout`, `stderr`, `error`, `execution_time_ms`, `runtime_version`

---

## ucm/text-to-speech — $0.01/call

Convert text to natural speech audio using Kokoro TTS. Returns base64 audio.

**Endpoint: `speak`**
```json
{ "input": "Hello, world!", "voice": "af_heart", "language": "en" }
```
- `input` (required): Text to convert
- `voice` (optional): default `"af_heart"` — voice must match language
- `language` (optional): `en` | `zh` | `ja` | `ko` | `fr` | `de` | `es` | `pt`, default `"en"`
- `response_format` (optional): `"mp3"` | `"wav"`, default `"mp3"`

**Voice prefixes by language:**
- English (US): `af_*` / `am_*` (e.g. `af_heart`, `am_adam`)
- English (UK): `bf_*` / `bm_*` (e.g. `bf_alice`, `bm_george`)
- Chinese: `zf_*` / `zm_*` (e.g. `zf_xiaobei`, `zm_yunxi`)
- Japanese: `jf_*` / `jm_*` (e.g. `jf_alpha`, `jm_kumo`)
- Spanish: `ef_*` / `em_*`
- French: `ff_*`
- Hindi: `hf_*` / `hm_*`
- Italian: `if_*` / `im_*`
- Portuguese: `pf_*` / `pm_*`

---

## ucm/speech-to-text — $0.01/call

Transcribe audio to text using Whisper Large v3.

**Endpoint: `transcribe`**
```json
{ "audio_url": "https://example.com/audio.mp3" }
```
- `audio_base64` OR `audio_url` (one required): Audio input
- `filename` (optional): default `"audio.mp3"`
- `language` (optional): ISO 639-1 code, auto-detected if omitted
- `response_format` (optional): `"json"` | `"verbose_json"`, default `"verbose_json"`

---

## ucm/email — $0.01/send

Send emails with mandatory recipient verification. Agent must be claimed at dashboard.ucm.ai.

**Step 1 — `request-verification`** (FREE)
```json
{ "to": "user@example.com", "context": "Weekly report delivery" }
```

**Step 2 — `check-verification`** (FREE)
```json
{ "to": "user@example.com" }
```
Returns `status`: `"verified"` | `"pending"` | `"not_found"` | `"expired"` | `"unsubscribed"`

**Step 3 — `send`** ($0.01)
```json
{
  "to": "user@example.com",
  "subject": "Weekly Report",
  "body_text": "Here is your report...",
  "reply_to": "reply@yourdomain.com"
}
```

**`list-verified`** (FREE) — List all verified recipients.

---

## ucm/doc-convert — $0.02/call

Convert document URLs (PDF, DOCX, XLSX, CSV, XML) to clean markdown.

**Endpoint: `convert`**
```json
{ "url": "https://example.com/report.pdf" }
```
- `url` (required): Document URL (not file upload)

---

## ucm/us-stock — $0.01/call

US stock market data via Finnhub. 11 endpoints:

| Endpoint | Required Params | Description |
|----------|----------------|-------------|
| `quote` | `symbol` | Real-time price, change, high/low |
| `profile` | `symbol` | Company name, industry, market cap, IPO date |
| `search` | `q` | Search stocks by name or symbol |
| `news` | `symbol`, `from`, `to` | Company news articles |
| `metrics` | `symbol` | PE ratio, 52-week high/low, market cap |
| `peers` | `symbol` | List of peer companies |
| `earnings` | `symbol` | Historical EPS actual vs estimate |
| `recommendation` | `symbol` | Analyst buy/hold/sell trends |
| `insider` | `symbol` | Insider buy/sell transactions |
| `filings` | `symbol` | SEC filings (10-K, 10-Q, 8-K) |
| `ipo-calendar` | — | Upcoming IPOs (`from`, `to` optional) |

**Example:**
```json
{ "service_id": "ucm/us-stock", "endpoint": "quote", "body": { "symbol": "AAPL" } }
```

---

## ucm/cn-finance — $0.01/call

China financial data via Tushare Pro. 26 endpoints:

### Stock Data
| Endpoint | Description | Key Params |
|----------|-------------|------------|
| `daily` | Daily OHLCV | `ts_code`, `trade_date`, `start_date`, `end_date` |
| `weekly` | Weekly OHLCV | Same as daily |
| `monthly` | Monthly OHLCV | Same as daily |
| `stock-basic` | List A-share stocks | `exchange` (SSE/SZSE), `list_status` |
| `daily-basic` | PE, PB, turnover, market cap | `ts_code`, `trade_date` |
| `adj-factor` | Price adjustment factors | `ts_code`, `trade_date` |
| `trade-cal` | Trading calendar | `exchange`, `start_date`, `end_date` |
| `query` | Raw Tushare API (legacy) | `api_name`, `params` |

### Financials
| Endpoint | Description |
|----------|-------------|
| `income` | Income statement |
| `balancesheet` | Balance sheet |
| `cashflow` | Cash flow statement |
| `forecast` | Earnings forecast |
| `express` | Earnings express |
| `dividend` | Dividends and bonus shares |
| `fina-indicator` | ROE, ROA, debt ratio, gross margin |

### Indices & Funds
| Endpoint | Description |
|----------|-------------|
| `index-basic` | List indices (SSE 50, CSI 300) |
| `index-daily` | Daily OHLCV for indices |
| `fund-basic` | Mutual fund list |
| `fund-nav` | Fund NAV history |
| `fund-daily` | ETF daily OHLCV |

### Macro
| Endpoint | Description |
|----------|-------------|
| `shibor` | Interbank rate |
| `lpr` | Loan Prime Rate |
| `cn-gdp` | GDP (quarterly) |
| `cn-cpi` | Consumer Price Index (monthly) |
| `cn-ppi` | Producer Price Index (monthly) |
| `cn-m` | Money supply M0/M1/M2 |

**Example:**
```json
{ "service_id": "ucm/cn-finance", "endpoint": "daily", "body": { "ts_code": "000001.SZ", "start_date": "20240101", "end_date": "20240131" } }
```

---

## ucm/weather — FREE

Global weather data powered by Open-Meteo. All endpoints free.

| Endpoint | Description | Key Params |
|----------|-------------|------------|
| `current` | Current temperature, humidity, wind | `city` or `latitude`+`longitude` |
| `forecast` | Daily forecast up to 16 days | `city`, `days` (1-16) |
| `air-quality` | PM2.5, PM10, AQI | `city` or coordinates |
| `geocode` | City name to coordinates | `city` |

**Example:**
```json
{ "service_id": "ucm/weather", "endpoint": "current", "body": { "city": "Tokyo" } }
```
