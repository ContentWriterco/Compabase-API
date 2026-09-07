# 🇵🇱 Polish Companies Data API (KRS & CEIDG) — Compabase

[![API Docs](https://img.shields.io/badge/Docs-Interactive_Redoc-blue)](https://compabase.com/docs/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![OpenAPI](https://img.shields.io/badge/OpenAPI-3.1-orange)](v1.yaml)
[![Free Tier](https://img.shields.io/badge/Free_Tier-100_req%2Fmo-brightgreen)](https://compabase.com/api-keys)

**Compabase** is a developer-first REST API to search, filter, and analyze over **3 million Polish companies** from the **KRS (Krajowy Rejestr Sądowy — National Court Register)** and **CEIDG (Centralna Ewidencja i Informacja o Działalności Gospodarczej)**. Access normalized company profiles, multi-year financial statements (P&L, balance sheet, ratios), KRS ownership structures, management roles, peer rankings, company connections, GPW listings, and watchlists — all from a single consistent JSON API.

![Compabase API Data Flow](sankey-chart.png)

---

## Quick Start

```bash
# Get a full company profile by KRS number
curl "https://compabase.com/api/v1/companies/krs/0000028860" \
  -H "X-API-Key: YOUR_API_KEY"
```

<details>
<summary>📄 Show example response</summary>

```json
{
  "company": {
    "entity_id": "ad2ae683-ccbd-49c6-a85c-648024435cb5",
    "registry_number": "0000028860",
    "company_name": "ORLEN SPÓŁKA AKCYJNA",
    "company_name_display": "Orlen",
    "nip": "7740001454",
    "regon": "61018820100000",
    "legal_form": "SPÓŁKA AKCYJNA",
    "city": "PŁOCK",
    "region": "mazowieckie",
    "full_address": "CHEMIKÓW 7, 09-411 Płock, PL",
    "activities": [
      { "code_full": "19.20.Z", "is_primary": true, "description": "Manufacture of refined petroleum products" }
    ]
  },
  "details": {
    "contacts": { "email": "zarzad@orlen.pl", "website": "orlen.pl" }
  },
  "financials": {
    "years": ["2024", "2023"],
    "byYear": {
      "2024": [{
        "revenue_total": 304668000000,
        "revenue_total_usd": 76338762214.98,
        "revenue_total_eur": 70672233820.46,
        "profit_net": 1383000000,
        "ebit": 2484000000,
        "total_assets": 255368000000,
        "equity": 146689000000,
        "roa": 0.54,
        "net_margin": 0.45
      }]
    }
  },
  "roles": [
    { "party": { "display_name": "Alejandro Martinez Ruiz" }, "role_name": "President of the Management Board" }
  ],
  "ownership": [
    { "owner": { "display_name": "Skarb Państwa" }, "holding_percent": 49.9, "holding_value_pln": 4200000000 }
  ]
}
```

</details>

---

## What You Can Build

- **Polish B2B database API** — search and filter the full KRS company register by industry, location, revenue, and legal form
- **KRS search by PKD code** — find all active companies in a specific industry sector across Poland
- **Validate KRS number** — check legal status (active, bankruptcy, liquidation) in a single lightweight call
- **Polish companies financial statements API** — pull multi-year P&L, balance sheet, and ratio data (PLN / USD / EUR)
- **KRS ownership structure API** — explore shareholder structures and management boards for due diligence
- **Company revenue data Poland** — rank companies by revenue, EBITDA, or total assets within any sector or region
- **Active company verification Poland** — integrate status checks into onboarding or KYC flows
- **Lead generation & B2B enrichment** — filter by `has_email`, `has_website`, voivodeship, and financial thresholds

---

## Endpoints

| Method | Path | Description |
|--------|------|-------------|
| `GET` | `/companies` | Search & filter Polish companies (3M+ records, KRS & CEIDG) |
| `GET` | `/companies/count` | Count matching companies without fetching rows |
| `GET` | `/companies/export` | Same filters as `/companies`, page size up to **500** (cursor paging) |
| `GET` | `/companies/krs/{krs}` | Full company profile by KRS number |
| `GET` | `/companies/krs/{krs}/financial-statements` | Multi-year financial statements only |
| `GET` | `/companies/krs/{krs}/financial-documents` | Inventory of filed financial documents (metadata) |
| `GET` | `/companies/krs/{krs}/structure-people` | Ownership & management structure only |
| `GET` | `/companies/krs/{krs}/connections` | Related companies and people (shared roles / ownership) |
| `GET` | `/companies/krs/{krs}/statistics` | Peer benchmarks and rank (PKD / region / Poland) |
| `GET` | `/companies/krs/{krs}/news` | Company news and UGC (where published) |
| `GET` | `/companies/krs/{krs}/gpw` | Warsaw Stock Exchange listing + delayed quotes |
| `GET` | `/companies/krs/{krs}/status` | Lightweight legal status check |
| `GET` | `/ceidg/nip/{nip}` | CEIDG sole-proprietor profile by NIP |
| `GET` | `/persons/search` | Search natural persons by name |
| `GET` | `/persons/{personId}/connections` | Companies linked to a person |
| `GET` | `/watchlist` | List watchlist for the account on the API key |
| `POST` | `/watchlist/krs/{krs}` | Add a company to the watchlist |
| `DELETE` | `/watchlist/krs/{krs}` | Remove a company from the watchlist |
| `GET` | `/usage` | Monthly quota usage for the current API key |
| `GET` | `/health` | Public uptime check (no key required) |

---

### `GET /companies` — Search & Filter Polish Companies

Search over **3 million Polish companies** from KRS and CEIDG using combined AND filters.
Returns paginated results with financial metrics per row. Supports both offset and cursor-based pagination.

```bash
# Active manufacturing companies in Mazovia with revenue > 10M PLN
curl "https://compabase.com/api/v1/companies?pkd=28&voivodeship=mazowieckie&revenue_min=10000000&status_active=true&limit=10" \
  -H "X-API-Key: YOUR_API_KEY"
```

**Pagination**

| Mode | Parameters | Best for |
|------|-----------|----------|
| Offset | `offset`, `limit` | Interactive browsing, UI tables |
| Cursor | `cursor`, `limit` | Deep export, large dataset traversal |

> `offset` and `cursor` are mutually exclusive. Use cursor mode for deterministic deep paging; `pagination.total` may be `-1` in cursor mode — rely on `hasMore` instead.

<details>
<summary>📋 Show all available filter parameters</summary>

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Full-text company name search |
| `keywords` | string | Keyword token search |
| `pkd` | string | PKD industry code — e.g. `46.90.Z`. Combine with `primary_only=true` to match primary activity only |
| `voivodeship` | string | Polish voivodeship, lowercase — e.g. `mazowieckie`, `śląskie`, `wielkopolskie` |
| `city` | string | City name, uppercase — e.g. `WARSZAWA`, `KRAKÓW` |
| `postal_code` | string | Postal code prefix |
| `legal_form` | string | Legal form — e.g. `SPÓŁKA AKCYJNA`, `SPÓŁKA Z OGRANICZONĄ ODPOWIEDZIALNOŚCIĄ` |
| `status_active` | bool | `true` = active companies only, `false` = inactive only |
| `has_email` | bool | Filter by email presence |
| `has_website` | bool | Filter by website presence |
| `currency` | string | Currency for financial thresholds: `PLN` (default), `USD`, `EUR` |
| `revenue_min` / `revenue_max` | number | Total revenue range |
| `revenue_operating_min` / `revenue_operating_max` | number | Operating revenue range |
| `profit_net_min` / `profit_net_max` | number | Net profit range |
| `profit_operating_min` / `profit_operating_max` | number | Operating profit range |
| `ebitda_min` / `ebitda_max` | number | EBITDA range |
| `total_assets_min` / `total_assets_max` | number | Total assets range |
| `capital_min` / `capital_max` | number | Share capital range |
| `estimated_value_min` / `estimated_value_max` | number | Estimated company value range |
| `cost_wages_min` / `cost_wages_max` | number | Wage cost range |
| `cost_amortization_min` / `cost_amortization_max` | number | Amortization cost range |
| `income_tax_min` / `income_tax_max` | number | Income tax range |
| `registration_year_min` / `registration_year_max` | integer | Registration year range |
| `last_report_year_min` / `last_report_year_max` | integer | Last financial report year range |

> Financial range filters are evaluated against the **latest indexed financial snapshot** per company. Companies with `null` for a filtered metric do not match that filter.

</details>

---

### `GET /companies/count` — Count Matching Companies

Returns a single `count` integer for the same filter set as `/companies` — without fetching rows.
Ideal for faceted search UIs, dashboards, and estimating export volume before paginating.

```bash
curl "https://compabase.com/api/v1/companies/count?pkd=46.90.Z&voivodeship=śląskie&status_active=true" \
  -H "X-API-Key: YOUR_API_KEY"
# → { "count": 3142 }
```

---

### `GET /companies/krs/{krs}` — Full Company Profile

Returns the complete profile for a **Polish company identified by KRS number**.
Leading zeros are optional — the API normalizes the value to 10 digits server-side.

**Response sections:**

| Key | Contents |
|-----|----------|
| `company` | Core identifiers, address, PKD activities, display fields |
| `details` | Contacts (email, website), suspension/deletion flags, filings |
| `financials` | Multi-year P&L + balance sheet metrics in PLN / USD / EUR |
| `roles` | Management board, supervisory board, proxies |
| `ownership` | Shareholder structure — holding %, share count, values in PLN / USD / EUR |
| `companySummary` | AI-generated short description (PL / EN / DE) |
| `similarCompanies` | Related companies by industry and size |

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /companies/krs/{krs}/financial-statements` — Financial Statements

**Polish companies financial statements API** — returns all financial metrics grouped by reporting year,
without any profile or metadata. Values are available in PLN, USD and EUR.

<details>
<summary>📊 Show available financial fields</summary>

**P&L**
`revenue_total`, `revenue_operating`, `profit_net`, `profit_gross`, `ebit`, `ebitda`,
`operating_costs_total`, `financial_income`, `financial_costs`, `income_tax`

**Balance sheet**
`total_assets`, `fixed_assets`, `current_assets`, `equity`, `liabilities_and_provisions`

**Ratios**
`roa`, `net_margin`, `asset_turnover`

All fields are also available with `_usd` and `_eur` suffixes.

</details>

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860/financial-statements" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /companies/krs/{krs}/structure-people` — Ownership & Management Structure

Returns `roles` (management board, supervisory board, proxies) and `ownership` (shareholders
with holding percentage, share count, and PLN / USD / EUR values) — without profile or financial data.
Purpose-built for **KRS ownership structure API** integrations and corporate due diligence workflows.

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860/structure-people" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /companies/krs/{krs}/status` — Legal Status Check

Lightweight endpoint for **active company verification in Poland**. Returns `status`
(`active`, `bankruptcy`, `liquidation`) and `is_open` flag. Validate KRS numbers or monitor
status changes without pulling the full profile.

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860/status" \
  -H "X-API-Key: YOUR_API_KEY"
# → { "krs": "0000028860", "status": "active", "label": "Active", "is_open": true }
```

---

### `GET /companies/export` — Bulk Search Page

Same filters as `GET /companies`, with page size up to **500**. Contact emails are included when available. Page with `cursor` (`cursor` = last row `entity_id`). Each page consumes **one** `api_requests` unit — not the portal/MCP `export_companies` meter.

```bash
curl "https://compabase.com/api/v1/companies/export?pkd=62&status_active=true&limit=500" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /companies/krs/{krs}/financial-documents` — Document Inventory

Metadata list of financial documents (periods, types, names). Structured metrics stay on the profile and `/financial-statements` endpoints.

---

### `GET /companies/krs/{krs}/connections` — Company Graph

Related companies and people linked through shared roles or ownership (same payload as the profile Connections UI).

---

### `GET /companies/krs/{krs}/statistics` — Peer Rankings

Industry / region / country peer benchmarks and the company's rank. Use when `hasStatistics` on the full profile is `true`.

---

### `GET /companies/krs/{krs}/gpw` — Warsaw Stock Exchange

Listing snapshot for GPW Main Market, NewConnect, or GlobalConnect: ticker, ISIN, last close, market cap, EPS, P/E, dividend, recent daily bars. Quotes delayed ~15 minutes, PLN. `gpw` is `null` when the company is not listed. Not an official GPW licensed feed.

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860/gpw" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /ceidg/nip/{nip}` — CEIDG Profile

Sole-proprietor profile by **NIP**. Contact email/phone are returned for authenticated API keys when present and allowed. Personal owner fields follow the same RODO gating as the website.

```bash
curl "https://compabase.com/api/v1/ceidg/nip/5252344078" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /persons/search` + `GET /persons/{personId}/connections`

Search natural persons by first/last name. Then load companies linked to a `person_id` (board, ownership, proxies). Identification is name-based — no PESEL.

---

### Watchlist

`GET /watchlist` lists companies followed by the **user account linked to the API key** (the key must have `user_id`). `POST` / `DELETE` `/watchlist/krs/{krs}` add or remove a company.

---

### `GET /usage` — Quota Usage

Returns current-month usage for the authenticated key. Does **not** consume quota.

```bash
curl "https://compabase.com/api/v1/usage" \
  -H "X-API-Key: YOUR_API_KEY"
# → { "used": 42, "limit": 5000, "remaining": 4958, "monthUtc": "2026-06", "resetsAtUtc": "2026-07-01T00:00:00Z" }
```

---

### `GET /health` — API Health

Public endpoint (no API key required). Returns `{ "status": "ok", "api": "v1", "timestamp": "..." }`.

---

## Authentication

Send your key in one of the supported headers:

```
X-API-Key: <your_key>
Authorization: Bearer <your_key>
```

[Get a Free API Key instantly →](https://compabase.com/api-keys)

---

## Plans & Quota

Each API key has a monthly `api_requests` quota that resets with the **UTC calendar month** (`GET /usage` returns `monthUtc` / `resetsAtUtc`). Authenticated requests include **contact email** on profiles and search rows when available.

| Plan | Requests / month |
|------|-----------------|
| Free | 100 |
| Pro | 5 000 |
| Scale | 100 000 |
| Enterprise | Custom |

- `GET /usage` does **not** consume quota
- `401` responses (auth fails before processing) do **not** consume quota
- `429` responses (quota exhausted) do **not** consume additional quota
- All other authenticated responses (`200`, `400`, `404`) **do** consume quota

---

## OpenAPI Specification

- `v1.yaml` — source of truth for the API contract (OpenAPI 3.1)
- Base URL: `https://compabase.com/api/v1/`

[Read the Full Interactive API Documentation →](https://compabase.com/docs/)

---

## Related

- **[Compabase MCP Server](https://github.com/ContentWriterco/compabase-mcp)** — connect Claude, Cursor, and other AI assistants directly to Polish company data via the Model Context Protocol
- **[Interactive MCP docs](https://compabase.com/docs/mcp/)** — tools reference and client setup

---

## License

MIT
