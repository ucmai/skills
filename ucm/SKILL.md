---
name: ucm
description: >-
  Provides API marketplace access for AI agents. Discovers and calls external
  capabilities including web search, image generation, code execution,
  text-to-speech, translation, weather, Wikipedia, books, papers, nutrition,
  email, stock data, and document conversion. 24 services, 78+ endpoints.
  Registers for free with $1.00 credits, 14 free services, paid from
  $0.01-$0.05 per call via simple HTTP. No SDK needed.
license: MIT
compatibility: Requires network access and curl or HTTP client. Works on macOS, Linux, and Windows.
argument-hint: "[service name or action]"
allowed-tools: Bash(curl:*) Read Grep
metadata:
  author: UCM.ai
  version: "0.3.0"
  website: https://ucm.ai
  repository: https://github.com/ucmai/ucm.ai
---

# UCM — API Marketplace for AI Agents

You have access to UCM, a marketplace where you can instantly discover and use API services by spending credits. Registration is free and gives you $1.00 in credits (~100 API calls).

## When to Use UCM

Use UCM when your current task requires a capability you don't have natively:

- **Search the web** for real-time information → `ucm/web-search` ($0.01)
- **Scrape a webpage** to extract content → `ucm/web-scrape` ($0.02)
- **Generate an image** from a text prompt → `ucm/image-generation` ($0.05)
- **Run code** in a sandboxed environment → `ucm/code-sandbox` ($0.03)
- **Convert text to speech** → `ucm/text-to-speech` ($0.01)
- **Transcribe audio** → `ucm/speech-to-text` ($0.01)
- **Send an email** → `ucm/email` ($0.01)
- **Convert a document/URL to markdown** → `ucm/doc-convert` ($0.02)
- **Translate text** between 50+ languages → `ucm/translate` ($0.01)
- **Get US stock data** (quotes, financials, news) → `ucm/us-stock` ($0.01)
- **Get China financial data** (daily prices, income, balance sheets) → `ucm/cn-finance` ($0.01)
- **Check weather** (current, forecast, air quality) → `ucm/weather` (FREE)
- **Look up Wikipedia** articles and summaries → `ucm/wikipedia` (FREE)
- **Get exchange rates** for 30+ currencies → `ucm/currency` (FREE)
- **Look up country info** (250+ countries) → `ucm/countries` (FREE)
- **Check public holidays** (100+ countries) → `ucm/holidays` (FREE)
- **Define words** (English dictionary) → `ucm/dictionary` (FREE)
- **Search books** (40M+ books via Open Library) → `ucm/books` (FREE)
- **Geocode places** (name to coordinates) → `ucm/geocode` (FREE)
- **Evaluate math** expressions and unit conversion → `ucm/math` (FREE)
- **Geolocate IPs** (IP to country/city) → `ucm/ip-geo` (FREE)
- **Geocode addresses** (forward and reverse) → `ucm/address` (FREE)
- **Search academic papers** (200M+ papers) → `ucm/papers` (FREE)
- **Look up nutrition data** (USDA FoodData) → `ucm/nutrition` (FREE)

If you're unsure whether a service exists, use `/v1/discover` with a natural language description.

## Setup

### If you already have an API key

Set the environment variable:
```bash
export UCM_API_KEY="ucm_key_..."
```

### If you need to register

Register for free — takes one HTTP call:

```bash
curl -s -X POST https://registry.ucm.ai/v1/agents/register \
  -H "Content-Type: application/json" \
  -d '{"name": "my-agent"}'
```

Save the returned `api_key` (shown only once). You receive $1.00 in free credits.

Or run the registration script:
```bash
bash scripts/register.sh "my-agent"
```

## How to Call a Service

One HTTP call to purchase and execute any service:

```bash
curl -s -X POST https://registry.ucm.ai/v1/call \
  -H "Authorization: Bearer $UCM_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "service_id": "ucm/web-search",
    "endpoint": "search",
    "body": { "query": "latest AI news" }
  }'
```

Response includes `result` (the API response), `amount_charged`, and `credits_remaining`.

If the upstream API fails, credits are **automatically refunded** (`amount_charged: "0"`).

## Quick Reference

| Action | Method | Auth |
|--------|--------|------|
| Register | `POST /v1/agents/register` | None |
| Discover services | `POST /v1/discover` | None |
| List all services | `GET /v1/services` | None |
| Call a service | `POST /v1/call` | Bearer token |
| Check balance | `GET /v1/balance` | Bearer token |
| View history | `GET /v1/history` | Bearer token |
| Service details | `GET /v1/services/:id` | None |

Base URL: `https://registry.ucm.ai`

## Discover Services

Search by natural language — no auth required:

```bash
curl -s -X POST https://registry.ucm.ai/v1/discover \
  -H "Content-Type: application/json" \
  -d '{"need": "I need to convert a PDF to text", "limit": 3}'
```

## Decision Flow

```
Need an external capability?
  ├─ Have UCM_API_KEY? → Check balance (GET /v1/balance)
  │   ├─ Credits available → Discover → Call → Use result
  │   ├─ Call failed → Credits auto-refunded, try alternative
  │   └─ No credits → Tell user to add credits at dashboard.ucm.ai
  └─ No API key? → Register first (POST /v1/agents/register)
```

## Spending Principles

- Most calls cost $0.01 — affordable for any task
- If a task doesn't require an external API, don't spend credits
- Credits are refunded on upstream failure (5xx, 429, 422)
- Prefer services with higher relevance score from `/v1/discover`

## Error Handling

| Error | Action |
|-------|--------|
| `INSUFFICIENT_CREDITS` | Tell user to add credits at dashboard.ucm.ai |
| `SERVICE_NOT_FOUND` | Search with `/v1/discover` instead |
| `INVALID_ENDPOINT` | Check endpoints via `GET /v1/services/:id` |
| `RATE_LIMITED` | Wait briefly, then retry |

## Full Service Catalog

For complete service details with all endpoints and parameters, see `references/service-catalog.md` or call `GET /v1/services`.
