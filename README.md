<!-- Generated from the SocQ API catalog and docs. Do not edit by hand. -->
# TikTok Creative Center API

[![SocQ TikTok Creative Center API](https://img.shields.io/badge/API-SocQ%20TikTok%20Creative%20Center%20API-F64C31)](https://socq.ai/apis/tiktok-creative-center?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api)
[![API documentation](https://img.shields.io/badge/Docs-docs.socq.ai-111827)](https://docs.socq.ai/api-manual/tiktok-creative-center/overview)
[![License: MIT](https://img.shields.io/badge/License-MIT-2563EB)](LICENSE)
[![Check examples](https://github.com/SocQAPI/tiktok-creative-center-api/actions/workflows/check.yml/badge.svg)](https://github.com/SocQAPI/tiktok-creative-center-api/actions/workflows/check.yml)

6 endpoints for public TikTok Creative Center data through one SocQ API key, with runnable cURL, Node.js and Python examples and a valid request body for every endpoint.

[Browse TikTok Creative Center APIs](https://socq.ai/apis/tiktok-creative-center?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api) · [Get an API key](https://socq.ai/dashboard/api-key?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api) · [Documentation](https://docs.socq.ai/api-manual/tiktok-creative-center/overview) · [All SocQ examples](https://github.com/SocQAPI/socq-examples)

## Endpoints

Prices are in SocQ credits; 1 credit = $0.005.

| Endpoint | publicId | What it returns | Price | Launched |
| --- | --- | --- | --- | --- |
| [TikTok Creative Center Top Ads API](#tiktok-creative-center-top-ads-api) | `tiktok-creative-center/top-ads` | Browse TikTok Creative Center's top-performing ads with optional keyword, period, and sort filters, returning captions, industry, objective, CTR, likes, and video files. | 0.5 credits ($0.0025) per result | 2026-11-17 |
| [TikTok Creative Center Ad Analysis API](#tiktok-creative-center-ad-analysis-api) | `tiktok-creative-center/ad-analysis` | Analyze one TikTok Creative Center top ad by its ad ID, returning ad details, an impressions percentile, and second-by-second click-through and retention curves. | 1 credit ($0.005) per call | 2026-11-17 |
| [TikTok Creative Center Trending Hashtags API](#tiktok-creative-center-trending-hashtags-api) | `tiktok-creative-center/trending-hashtags` | Collect the hashtags trending in a country from TikTok Creative Center, returning rank, post and view counts, and a daily popularity curve over 7, 30, or 90 days. | 0.5 credits ($0.0025) per result | 2026-11-17 |
| [TikTok Creative Center Hashtag Insights API](#tiktok-creative-center-hashtag-insights-api) | `tiktok-creative-center/trending-hashtag-detail` | Analyze one trending TikTok hashtag by its Creative Center ID, returning post and view totals, a daily popularity curve, top countries by index, and example video IDs. | 0.5 credits ($0.0025) per call | 2026-11-17 |
| [TikTok Creative Center Top Videos API](#tiktok-creative-center-top-videos-api) | `tiktok-creative-center/top-videos` | Collect TikTok Creative Center's chart of top-performing videos for a country, returning rank, caption, creator, chart-period and lifetime views, and engagement rates. | 0.5 credits ($0.0025) per result | 2026-11-17 |
| [TikTok Creator Marketplace API](#tiktok-creator-marketplace-api) | `tiktok-creative-center/creator-cards` | Retrieve TikTok Creator Marketplace cards by creator ID, returning follower age, gender, country, and device breakdowns, median views, engagement rate, and starting rates. | 0.5 credits ($0.0025) per result | 2026-11-17 |

## Quick start

1. Create an API key at [socq.ai/dashboard/api-key](https://socq.ai/dashboard/api-key?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api).
2. Export it. The examples read only `SOCQ_API_KEY`.

```bash
git clone https://github.com/SocQAPI/tiktok-creative-center-api.git
cd tiktok-creative-center-api
export SOCQ_API_KEY="your-api-key"
```

3. Run any endpoint by its publicId. Without a second argument the script sends `payloads/<slug>.json`; pass a JSON file or an inline JSON object to change the input.

```bash
# Node.js 20+
node examples/node/socq.mjs tiktok-creative-center/top-ads

# Python 3.8+ (standard library only)
python3 examples/python/socq.py tiktok-creative-center/top-ads payloads/top-ads.json

# cURL + jq
bash examples/curl/socq.sh tiktok-creative-center/top-ads '{"query":"skincare","time_range":"30d","sort_by":"likes"}'
```

Each script submits the task, polls it until it succeeds or fails, follows every result page, and writes the records to `output/<slug>-<task_id>.json`. Run `npm run check` to validate the payloads and scripts offline.

Never expose `SOCQ_API_KEY` in browser code, mobile apps, public repositories, screenshots, fixtures or logs.

## How the API works

Base URL `https://api.socq.ai`. Every request sends the API key as a bearer token:

```http
Authorization: Bearer <SOCQ_API_KEY>
Content-Type: application/json
```

All endpoints are asynchronous:

```text
POST /v1/<publicId>  ->  data.task_id  ->  GET /v1/tasks/{task_id}  ->  queued | running | succeeded | failed
```

1. **Submit** the JSON body to `POST https://api.socq.ai/v1/<publicId>`. The response returns `data.task_id` immediately; it means the task was created, not that data is ready.
2. **Poll** `GET https://api.socq.ai/v1/tasks/{task_id}` until `data.status` is `succeeded` or `failed` (a failed task carries `data.error_message`).
3. **Read results.** A succeeded task returns normalized records in `data.results.items`. Use `limit` (default 50, max 500) and pass `data.results.next_cursor` as `cursor` while `data.results.has_more` is true. `fields` selects up to 50 comma-separated fields or dot paths.
4. **Raw files (optional).** `GET https://api.socq.ai/v1/tasks/{task_id}/files` lists raw JSONL exports.

```bash
curl "https://api.socq.ai/v1/tasks/$TASK_ID?limit=100" \
  -H "Authorization: Bearer $SOCQ_API_KEY"

curl "https://api.socq.ai/v1/tasks/$TASK_ID?limit=100&cursor=$NEXT_CURSOR" \
  -H "Authorization: Bearer $SOCQ_API_KEY"
```

`callback_url` (where an endpoint accepts it) makes SocQ POST to your URL when the task reaches a terminal state; keep polling as a fallback.

**Billing.** A submit may reserve credits from `results_limit` or the number of submitted inputs; when the task finishes SocQ settles the charge from the actual result count and returns unused reserved credits. `402` means insufficient credits.

**Errors.** `400` invalid body or unsupported field, `401` missing or invalid key, `402` insufficient credits, `403` IP allowlist or task owned by another account, `404` endpoint or task not found, `422` input validation failed, `429` rate or credit-window limit (respect `Retry-After`), `500` submission failed. A client-side timeout does not cancel a task: resume polling the same `task_id` instead of submitting again.

Reference: [Authentication](https://docs.socq.ai/api-manual/agent-api/authentication) · [Task status and results](https://docs.socq.ai/api-manual/agent-api/status) · [Task files](https://docs.socq.ai/api-manual/agent-api/files) · [Callbacks](https://docs.socq.ai/api-manual/agent-api/callbacks) · [Errors](https://docs.socq.ai/api-manual/agent-api/errors)

## Endpoint reference

### TikTok Creative Center Top Ads API

Browse TikTok Creative Center's top-performing ads with optional keyword, period, and sort filters, returning captions, industry, objective, CTR, likes, and video files.

- **Request:** `POST https://api.socq.ai/v1/tiktok-creative-center/top-ads`
- **publicId:** `tiktok-creative-center/top-ads`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-11-17
- **Example body:** [`payloads/top-ads.json`](payloads/top-ads.json)

Browse the TikTok ads that perform best, as ranked in TikTok Creative Center. Each ad comes with its caption, industry, campaign objective, click-through rate, likes and the full video, which makes the results a working swipe file for creative research. Add a `query` to focus on one product category or brand.

All fields are optional. query filters by keyword, time_range picks 7d, 30d (default) or 180d, and sort_by orders by relevance (default) or likes. results_limit defaults to 20 and accepts 1 to 300.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `query` | string | No | Optional keyword to match against ad captions and brands. |
| `results_limit` | integer | No | (value 1 to 300; default `20`) |
| `sort_by` | string | No | relevance for TikTok's own order, or likes for the most-liked ads first. (one of `likes`, `relevance`; default `"relevance"`) |
| `time_range` | string | No | Period the ads ran in: 7d, 30d or 180d. (one of `180d`, `30d`, `7d`; default `"30d"`) |

```bash
curl -X POST "https://api.socq.ai/v1/tiktok-creative-center/top-ads" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "query": "skincare",
  "time_range": "30d",
  "sort_by": "likes"
}'
```

Full docs: [https://docs.socq.ai/api-manual/tiktok-creative-center/top-ads](https://docs.socq.ai/api-manual/tiktok-creative-center/top-ads) · Landing page: [socq.ai/apis/tiktok-creative-center/top-ads](https://socq.ai/apis/tiktok-creative-center/top-ads?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api)

### TikTok Creative Center Ad Analysis API

Analyze one TikTok Creative Center top ad by its ad ID, returning ad details, an impressions percentile, and second-by-second click-through and retention curves.

- **Request:** `POST https://api.socq.ai/v1/tiktok-creative-center/ad-analysis`
- **publicId:** `tiktok-creative-center/ad-analysis`
- **Price:** 1 credit ($0.005) per call
- **Launched:** 2026-11-17
- **Example body:** [`payloads/ad-analysis.json`](payloads/ad-analysis.json)

Take one top ad apart second by second. Alongside the ad's details, the result traces how click-through interest and viewer retention change across the video, and shows where its impressions rank against comparable ads. Read the two curves together to locate the hook, the point where viewers drop off and the moment they act.

ad_id is required; use the id of a TikTok Creative Center Top Ads result. Returns one analysis, billed once.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `ad_id` | string | Yes | Ad ID from TikTok Creative Center Top Ads results. (pattern `^\\d{5,32}$`) |
| `callback_url` | string (uri) | No | - |

```bash
curl -X POST "https://api.socq.ai/v1/tiktok-creative-center/ad-analysis" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "ad_id": "7697084527089729557"
}'
```

Full docs: [https://docs.socq.ai/api-manual/tiktok-creative-center/ad-analysis](https://docs.socq.ai/api-manual/tiktok-creative-center/ad-analysis) · Landing page: [socq.ai/apis/tiktok-creative-center/ad-analysis](https://socq.ai/apis/tiktok-creative-center/ad-analysis?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api)

### TikTok Creative Center Trending Hashtags API

Collect the hashtags trending in a country from TikTok Creative Center, returning rank, post and view counts, and a daily popularity curve over 7, 30, or 90 days.

- **Request:** `POST https://api.socq.ai/v1/tiktok-creative-center/trending-hashtags`
- **publicId:** `tiktok-creative-center/trending-hashtags`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-11-17
- **Example body:** [`payloads/trending-hashtags.json`](payloads/trending-hashtags.json)

See which hashtags are climbing in a given country. The ranking gives each hashtag's position, post count, views and a daily popularity curve for the period, across markets such as US, GB and JP. Any tag worth a closer look can go straight into TikTok Creative Center Hashtag Insights.

All fields are optional. country (default US) and time_range (7d by default, or 30d or 90d) choose the ranking. results_limit defaults to 20 and accepts 1 to 100.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `country` | string | No | Two-letter country code of the market, such as US, GB or JP. (pattern `^[A-Za-z]{2}$`; default `"US"`) |
| `results_limit` | integer | No | (value 1 to 100; default `20`) |
| `time_range` | string | No | Period the ranking covers: 7d, 30d or 90d. (one of `30d`, `7d`, `90d`; default `"7d"`) |

```bash
curl -X POST "https://api.socq.ai/v1/tiktok-creative-center/trending-hashtags" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "country": "US",
  "time_range": "7d",
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/tiktok-creative-center/trending-hashtags](https://docs.socq.ai/api-manual/tiktok-creative-center/trending-hashtags) · Landing page: [socq.ai/apis/tiktok-creative-center/trending-hashtags](https://socq.ai/apis/tiktok-creative-center/trending-hashtags?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api)

### TikTok Creative Center Hashtag Insights API

Analyze one trending TikTok hashtag by its Creative Center ID, returning post and view totals, a daily popularity curve, top countries by index, and example video IDs.

- **Request:** `POST https://api.socq.ai/v1/tiktok-creative-center/trending-hashtag-detail`
- **publicId:** `tiktok-creative-center/trending-hashtag-detail`
- **Price:** 0.5 credits ($0.0025) per call
- **Launched:** 2026-11-17
- **Example body:** [`payloads/trending-hashtag-detail.json`](payloads/trending-hashtag-detail.json)

Dig into a single trending hashtag: its post and view totals, the daily popularity curve, the countries where it is most popular for their size and a set of example videos. The `id` from TikTok Creative Center Trending Hashtags plugs straight in.

hashtag_id is required; use the id of a TikTok Creative Center Trending Hashtags result together with the same country. time_range (default 7d) picks the period. Returns one result, billed once.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `country` | string | No | Two-letter country code of the market, such as US, GB or JP. (pattern `^[A-Za-z]{2}$`; default `"US"`) |
| `hashtag_id` | string | Yes | Hashtag ID from TikTok Creative Center Trending Hashtags results. (pattern `^\\d{5,32}$`) |
| `time_range` | string | No | Period to analyze: 7d, 30d or 90d. (one of `30d`, `7d`, `90d`; default `"7d"`) |

```bash
curl -X POST "https://api.socq.ai/v1/tiktok-creative-center/trending-hashtag-detail" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "hashtag_id": "7155204034885287979",
  "country": "US",
  "time_range": "7d"
}'
```

Full docs: [https://docs.socq.ai/api-manual/tiktok-creative-center/trending-hashtag-detail](https://docs.socq.ai/api-manual/tiktok-creative-center/trending-hashtag-detail) · Landing page: [socq.ai/apis/tiktok-creative-center/trending-hashtag-detail](https://socq.ai/apis/tiktok-creative-center/trending-hashtag-detail?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api)

### TikTok Creative Center Top Videos API

Collect TikTok Creative Center's chart of top-performing videos for a country, returning rank, caption, creator, chart-period and lifetime views, and engagement rates.

- **Request:** `POST https://api.socq.ai/v1/tiktok-creative-center/top-videos`
- **publicId:** `tiktok-creative-center/top-videos`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-11-17
- **Example body:** [`payloads/top-videos.json`](payloads/top-videos.json)

Pull TikTok's chart of top-performing videos for a country, ranked by views, engagement rate or six-second view rate. Each entry carries the caption, the creator, views in the chart period, lifetime views and engagement metrics. When a creator stands out, pass their `creator_id` to TikTok Creator Marketplace for audience and rate details.

All fields are optional. country defaults to US, period picks the daily (default), weekly or long_term chart, and sort_by ranks by views (default), engagement_rate or six_second_view_rate. The latest published chart is used. results_limit defaults to 20 and accepts 1 to 100.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `country` | string | No | Two-letter country code of the market, such as US, GB or JP. (pattern `^[A-Za-z]{2}$`; default `"US"`) |
| `period` | string | No | Chart to read: daily, weekly or long_term. (one of `daily`, `long_term`, `weekly`; default `"daily"`) |
| `results_limit` | integer | No | (value 1 to 100; default `20`) |
| `sort_by` | string | No | Ranking metric: views, engagement_rate or six_second_view_rate. (one of `engagement_rate`, `six_second_view_rate`, `views`; default `"views"`) |

```bash
curl -X POST "https://api.socq.ai/v1/tiktok-creative-center/top-videos" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "country": "US",
  "period": "daily",
  "results_limit": 20
}'
```

Full docs: [https://docs.socq.ai/api-manual/tiktok-creative-center/top-videos](https://docs.socq.ai/api-manual/tiktok-creative-center/top-videos) · Landing page: [socq.ai/apis/tiktok-creative-center/top-videos](https://socq.ai/apis/tiktok-creative-center/top-videos?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api)

### TikTok Creator Marketplace API

Retrieve TikTok Creator Marketplace cards by creator ID, returning follower age, gender, country, and device breakdowns, median views, engagement rate, and starting rates.

- **Request:** `POST https://api.socq.ai/v1/tiktok-creative-center/creator-cards`
- **publicId:** `tiktok-creative-center/creator-cards`
- **Price:** 0.5 credits ($0.0025) per result
- **Launched:** 2026-11-17
- **Example body:** [`payloads/creator-cards.json`](payloads/creator-cards.json)

Pull the Creator Marketplace card for creators you are evaluating: the age, gender, country and device mix of their followers, median views, engagement rate, follower growth and the starting rate they list for a sponsored video. Send the `creator_id` values from TikTok Creative Center Top Videos, up to 20 at a time.

creator_ids is required: 1 to 20 Creator Marketplace IDs, such as creator_id values from TikTok Creative Center Top Videos. Each creator with a card returns one result, billed per result.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `callback_url` | string (uri) | No | - |
| `creator_ids` | array of string | Yes | Creator Marketplace IDs, such as creator_id values from TikTok Creative Center Top Videos results. (items 1 to 20; pattern `^[0-9]{5,32}$`) |

```bash
curl -X POST "https://api.socq.ai/v1/tiktok-creative-center/creator-cards" \
  -H "Authorization: Bearer $SOCQ_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
  "creator_ids": [
    "7011198409688219653",
    "7070293872819044358"
  ]
}'
```

Full docs: [https://docs.socq.ai/api-manual/tiktok-creative-center/creator-cards](https://docs.socq.ai/api-manual/tiktok-creative-center/creator-cards) · Landing page: [socq.ai/apis/tiktok-creative-center/creator-cards](https://socq.ai/apis/tiktok-creative-center/creator-cards?utm_source=github&utm_medium=readme&utm_campaign=socq-tiktok-creative-center-api)

## Repository contents

| Path | Purpose |
| --- | --- |
| [`examples/curl/socq.sh`](examples/curl/socq.sh) | Submit, poll and paginate with cURL and jq |
| [`examples/node/socq.mjs`](examples/node/socq.mjs) | Same workflow in Node.js 20+ (no dependencies) |
| [`examples/python/socq.py`](examples/python/socq.py) | Same workflow in Python 3.8+ (standard library) |
| [`payloads/`](payloads) | One documented request body per endpoint |
| [`endpoints.json`](endpoints.json) | Endpoint list, prices and request schemas used by the scripts and checks |
| [`fixtures/`](fixtures) | Synthetic submit and task response shapes |
| [`docs/production-notes.md`](docs/production-notes.md) | Retries, timeouts, pagination and data handling |
| [`scripts/check.mjs`](scripts/check.mjs) | Offline check: payloads, syntax and secrets |

## Responsible use and platform scope

- Use only publicly accessible TikTok Creative Center data and the fields supported by each endpoint.
- Do not use the examples to access restricted content, login-only surfaces, authentication controls or non-public personal information.
- SocQ is not an official API of the represented platform and is not affiliated with or endorsed by it. Platform names and trademarks belong to their respective owners.
- Before production use, assess the laws, platform terms, privacy obligations and retention requirements that apply to your organization and use case.
- Collect only the fields needed for a defined purpose, restrict access, set retention periods, and support correction or deletion workflows where required.

This section describes the public-data boundary; it is not legal advice.

## License

[MIT](LICENSE)
