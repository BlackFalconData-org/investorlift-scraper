# Investorlift Scraper - Off-Market Real Estate Deals

Extract structured data from [investorlift.com](https://investorlift.com) — off-market investment property deals from investorlift.com with asking price, ARV estimate and repair range. Every deal adds address, size and photos plus the wholesaler name and company. Search by keyword or location, or paste a marketplace URL.

**[Investorlift Scraper - Off-Market Real Estate Deals on Apify →](https://apify.com/blackfalcondata/investorlift-scraper?fpr=1h3gvi)**

---

## 🚀 How to use this actor

> ### 💚 $5 free Apify credits — every month
> No credit card required. No commitment. Cancel anytime.

### 👉 [Sign up free on Apify →](https://console.apify.com/sign-up?fpr=1h3gvi)

1. **Click sign up** — pick GitHub, Google, or email; takes ~30 seconds
2. **Open this actor** — input is pre-filled with a working example
3. **Click Start** — export results as JSON, CSV, or Excel

Your **$5 monthly platform credit** is enough to run this actor right away — and again every month — scraping typically several hundred to several thousand results per run, depending on your input.

## Key features

**Search with filters** — Search by keyword and location. Filter by 🗂️ listing status, description format, and more.

**Detail enrichment** — Fetch full descriptions, contact information for each listing.

**Incremental mode** — Only get new or changed listings since your last run. Content hash per listing — no duplicates, no re-processing.

**Change classification** — Track unchanged, expired, cross-run duplicate detection across runs. Build audit trails of how listings evolve over time.

**Compact output** — Emit core fields only (AI-agent / MCP-friendly). Keeps response size small for LLM workflows.

**Description truncation** — Cap description length per ${NOUN} to control output size and cost.

**Result cap** — Stop after N listings (up to 1.000). Set to 0 for the full catalog.

**Export anywhere** — Download as JSON, CSV, or Excel. Stream via Apify API, webhooks, or integrations with Make, Zapier, Airbyte, Keboola.

**Structured data** — Every listing returns the same schema with consistent field naming. All fields always present — `null` when unavailable, never omitted.

---

## Use cases

**Data pipeline automation**
Integrate with your ETL pipeline to collect structured listings from investorlift.com on a schedule. Export to CSV, JSON, or directly to your database. Use compact mode to control output size.

**Market research**
Monitor listings, track trends, and analyze market dynamics with structured, deduplicated data from investorlift.com.

**Change monitoring**
Run daily or hourly in incremental mode to capture only new, updated, or expired ${NOUNS}. Perfect for price-tracking, churn analysis, and alerting pipelines.

**Lead generation**
Extract contact details alongside ${NOUNS} to build targeted outreach lists for sales teams, partnerships, or B2B prospecting.

**AI / LLM training data**
Structured JSON per listing is ready for RAG pipelines, embeddings, and agent workflows. Compact mode trims tokens for LLM context windows.

---

## Quick start

```json
{
  "query": "Houston",
  "maxResults": 100,
  "includeDetails": true,
  "descriptionMaxLength": 0,
  "compact": false,
  "descriptionFormat": "all",
  "excludeEmptyFields": false,
  "incrementalMode": false,
  "skipReposts": false,
  "emitUnchanged": false,
  "emitExpired": false,
  "notifyOnlyChanges": false,
  "notificationLimit": 5
}
```

---

## Input parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `query` | string | — | Keyword matched against the property title, city, county and ZIP — e.g. "Houston", "77008", "fix and flip". Use `*` to browse every active listing. Use a JSON array for several searches in one run (e.g. ["Houston","Dallas"]). Required if no startUrls provided. |
| `location` | string | — | Narrow results to a 2-letter US state code (e.g. "TX") or an exact city name (e.g. "Houston"). Leave empty for nationwide. JSON array accepted for several locations. |
| `maxResults` | integer | `100` | Maximum total results (0 = unlimited). |
| `startUrls` | array | `[]` | Paste raw search URLs from the target site. Each URL becomes its own search task; results are merged and deduped by listing ID across all URLs. When provided, startUrls REPLACE the query/location fields (each supported URL becomes one task; unsupported URLs are skipped, and if none is supported the run stops with an input error). |
| `scrapeMode` | enum | `"active_only"` | Which deals to return. `active_only` (default) = deals currently for sale. `historical` = pending and sold deals from the investorlift.com archive. `specific` = every status (for sale, pending, sold) — set automatically when you paste Deal IDs. |
| `dealIds` | array | `[]` | Scrape specific deals by their investorlift.com number (e.g. 12345) or deal link (investorlift.com/marketplace/p/12345). Works for active, pending and sold deals. |
| `states` | array | `[]` | 2-letter US state codes (e.g. TX, FL). Combined with Cities, a deal must match both. |
| `cities` | array | `[]` | City names (e.g. Houston). Case-insensitive exact match. Combined with States, a deal must match both. |
| `minPrice` | integer | — | Only deals with an asking price of at least this amount (USD, inclusive). Deals without a price are left out when a price filter is set. |
| `maxPrice` | integer | — | Only deals with an asking price of at most this amount (USD, inclusive). |
| `dateRangeStart` | string | — | Only deals listed on or after this date (YYYY-MM-DD, inclusive). In incremental mode a rolling window keeps the same state, so unchanged deals stay quiet, and deals outside the window are never marked expired. |
| `dateRangeEnd` | string | — | Only deals listed on or before this date (YYYY-MM-DD, inclusive). In incremental mode a rolling window keeps the same state, so unchanged deals stay quiet, and deals outside the window are never marked expired. |
| `findSellerContacts` | boolean | `false` | Find the wholesaler's own website and fill the email, phone and LinkedIn the listing left empty from its home page. The website is the one the listing links, else the company name's domain when that page names the company or shows the listing's email/phone, else the listing contact's own email domain. Values from the listing always win. `websiteSource` says how the website was found (`listing`, `email_domain`, `company_domain`) and `contactSource` where the contacts came from (`listing`, `company_site`, or both). |
| `includeDetails` | boolean | `true` | Fetch the full property page for every result: description, ARV/repair figures, all photos, wholesaler contact details and rating. Turn off for a faster, lighter run with search-level fields only. |
| `descriptionMaxLength` | integer | `0` | Truncate description to N chars. 0 = no truncation. |
| `compact` | boolean | `false` | Core fields only (for AI-agent/MCP workflows). |
| `descriptionFormat` | enum | `"all"` | Pick a single description representation. `all` keeps every variant; `text` / `html` / `markdown` drop the others. |
| `excludeEmptyFields` | boolean | `false` | Drop null, empty-string, and empty-array fields from each record before push. Smaller payloads for AI agents and dashboards. |
| `incrementalMode` | boolean | `false` | Compare against previous run state. stateKey is optional — defaults to a value derived from search inputs (queries, locations, startUrls) so different filter sets never share state. |
| `stateKey` | string | — | Optional. Stable identifier for the tracked search universe. Leave empty to auto-generate from search inputs. |
| `skipReposts` | boolean | `false` | When incremental, skip listings whose content matches an expired listing from a prior run (cross-run duplicate detection). |
| `emitUnchanged` | boolean | `false` | When incremental mode is on, also emit listings whose content has not changed since the last run. |
| `emitExpired` | boolean | `false` | When incremental mode is on, also emit listings that were seen before but are no longer found. |
| `notifyOnlyChanges` | boolean | `false` | When Incremental Mode is on, only send notifications for NEW and UPDATED listings. Has no effect outside incremental mode. |
| `notificationLimit` | integer | `5` | Maximum number of listings included in each notification message (1–20). |
| `telegramToken` | string | — | Telegram bot token (from @BotFather). Required for Telegram notifications. |
| `telegramChatId` | string | — | Telegram chat or channel ID (e.g. "-100123456789"). Required when telegramToken is set. |
| `discordWebhookUrl` | string | — | Discord incoming webhook URL. Server Settings → Integrations → Webhooks → New Webhook. |
| `slackWebhookUrl` | string | — | Slack incoming webhook URL. api.slack.com/messaging/webhooks. |
| `whatsappPhoneNumberId` | string | — | Your WhatsApp Business phone-number ID (numeric, from Meta dashboard). Required when whatsappAccessToken is set. |
| `whatsappAccessToken` | string | — | WhatsApp Cloud API permanent access token (System User token from Meta Business). Recipient must have messaged the business number within the last 24h (service-conversation window — free since Nov 2024). |
| `whatsappTo` | string | — | Recipient phone in E.164 format without + (e.g. "436641234567"). Recipient must have messaged your business number within last 24h. |
| `webhookUrl` | string | — | Receives a JSON POST with {metadata, items} after each run. Universal escape hatch for n8n / Make / Zapier / custom backends. |
| `webhookHeaders` | object | — | Optional JSON object of custom headers (e.g. {"Authorization":"Bearer ..."}). |
| `appConnector` | string | — | Optional. Pick a connected app under Settings → API & Integrations to receive your results (including any contact details). Best-effort across MCP connectors as Apify expands its catalog. |
| `mcpIssueTeam` | string | — | Only when the connected app is an issue tracker: the team (name or ID) the summary issue is created under, if that app requires one. |
| `lat` | number | — | GPS latitude. Pair with lon. |
| `lon` | number | — | GPS longitude. Pair with lat. |
| `radiusKm` | integer | — | Only return deals within this many kilometres of the Latitude/Longitude point. Set all three to search by radius; leave empty for no radius filter. |

---

## Output fields

Every listing returns the same 81-field schema. Missing values are `null` — never omitted.

- `id`
- `title`
- `status`
- `source`
- `city`
- `county`
- `state_code`
- `state`
- `zip`
- `latitude`
- `longitude`
- `price`
- `arv_estimate`
- `arv_percentage`
- `gross_margin`
- `bedrooms`
- `bathrooms`
- `sq_footage`
- `year_built`
- `lot_size`
- `property_type`
- `condition`
- `description`
- `days_on_il`
- `page_views`
- `hotness`
- `published_at`
- `expires_at`
- `img_url`
- `property_page_url`
- `property_url`
- `portalUrl`
- `account_id`
- `account_title`
- `wholesaler_company`
- `wholesaler_name`
- `wholesaler_rating`
- `wholesaler_review_count`
- `email`
- `phone`
- `website`
- `websiteSource`
- `linkedinUrl`
- `public_address`
- `full_address`
- `images`
- `images_count`
- `listing_email`
- `listing_phone`
- `property_image`
- `other_images`
- `other_images_available`
- `phone_number`
- `available`
- `status_changed_at`
- `unavailable_at`
- `created_at`
- `description_html`
- `buy_now_price`
- `repair_estimate_min`
- `repair_estimate_max`
- `occupancy`
- `half_bathrooms`
- `lot_size_unit`
- `min_emd`
- `parking_type`
- `condition_value`
- `open_houses_count`
- `is_verified`
- `tags`
- `matterport_url`
- `zestimate`
- `entry_fee`
- `slug`
- `scrapedAt`
- `listingId`
- `searchQuery`
- `contentQuality`
- `detailFetched`
- `contentHash`
- `changeType`

---

## Sample output

One object per listing. Here is a real example from a production run:

```json
{
  "id": "346811",
  "title": "New Assignable Contract - 8,080 SF Builder Opportunity in Pecan Park - Houston, TX",
  "status": "available",
  "source": "active",
  "city": "Houston",
  "county": "Harris County",
  "state_code": "TX",
  "state": "TX",
  "zip": "77087",
  "latitude": 29.7143763,
  "longitude": -95.2910488,
  "price": 104000
}
```

*Truncated — full records contain 81 fields. See Output fields for the complete schema.*

**[Try Investorlift Scraper - Off-Market Real Estate Deals now — $5 free credit, no credit card →](https://apify.com/blackfalcondata/investorlift-scraper?fpr=1h3gvi)**

---

## Pricing

Pay only for what you extract. No subscription required — Apify's free $5 credit covers thousands of results.

| Event | Price (USD) |
| --- | --- |
| Actor Start | $0.0045 |
| Result | $0.001971 |

See the [actor on Apify](https://apify.com/blackfalcondata/investorlift-scraper?fpr=1h3gvi) for current pricing.

---

## FAQ

**How do I scrape investorlift.com?**
Use this actor on Apify to extract structured data from investorlift.com. Configure your search query and filters in the input, then click Start — no coding required.

**How do I get investorlift.com data as JSON, CSV, or Excel?**
The actor writes each listing to Apify's dataset. Download as JSON, CSV, or Excel from the Console, stream via the API, or push to Make, Zapier, Airbyte, or Keboola.

**Is it legal to scrape investorlift.com?**
Web scraping of publicly available data is generally legal. This actor only accesses publicly visible information. Always check investorlift.com's terms of service for your specific use case.

**How much does it cost?**
Pay-per-event pricing — you only pay for ${NOUNS} extracted. Apify's free $5 credit is enough to run thousands of results before you pay anything.

**How does incremental mode work?**
Each listing gets a content hash. On subsequent runs, only new or changed listings are emitted — saving time, compute, and storage. Expired listings can be tracked separately.

**Do I need an API key or credentials?**
No. Just sign up for Apify, paste your input, and click Start. No credit card required.

---

## Related products by Black Falcon Data

[Browse all Black Falcon Data actors →](https://apify.com/blackfalcondata?fpr=1h3gvi)

---

## Getting started with Apify

New to Apify? [Create a free account with $5 credit](https://console.apify.com/sign-up?fpr=1h3gvi) — no credit card required.

1. Sign up — $5 platform credit included
2. Open [Investorlift Scraper - Off-Market Real Estate Deals](https://apify.com/blackfalcondata/investorlift-scraper?fpr=1h3gvi) and configure your input
3. Click **Start** — export results as JSON, CSV, or Excel

Need more later? [See Apify pricing](https://apify.com/pricing?fpr=1h3gvi).

---

## About Black Falcon Data

Black Falcon Data builds production-grade web scrapers for job boards and marketplace data. Browse our full actor catalog at [www.blackfalcondata.com](https://www.blackfalcondata.com).

---

*Last updated: 2026 10*
