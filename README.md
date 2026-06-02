# Compabase API

**Polish Companies API** — search, filter and analyze over 1 million Polish companies with
financial data, legal status, and ownership structure. All data sourced from KRS (National
Court Register) and normalized into a single, consistent JSON API.

## Quick Example

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860" \
  -H "X-API-Key: YOUR_API_KEY"
```

```json
{
  "company": {
    "entity_id": "ad2ae683-ccbd-49c6-a85c-648024435cb5",
    "registry_number": "0000028860",
    "company_name": "ORLEN SPÓŁKA AKCYJNA",
    "company_name_display": "Orlen",
    "nip": "7740001454",
    "legal_form": "SPÓŁKA AKCYJNA",
    "city": "PŁOCK",
    "region": "mazowieckie",
    "full_address": "CHEMIKÓW 7, 09-411 Płock, PL"
  },
  "financials": {
    "years": ["2024", "2023"],
    "byYear": {
      "2024": [{
        "revenue_total": 304668000000,
        "revenue_total_usd": 76338762214.98,
        "profit_net": 1383000000,
        "total_assets": 255368000000,
        "ebitda": 0,
        "roa": 0.54
      }]
    }
  }
}
```

## What you can do

- Search and filter companies by name, location, industry (PKD code), and financial metrics
- Retrieve full company profiles by KRS number
- Access multi-year financial statements with PLN / USD / EUR values
- Check legal status (active, liquidation, bankruptcy) in a single lightweight call
- Explore ownership structures and management roles
- Count matching companies before fetching pages (useful for UI facets and analytics)
- Monitor quota usage programmatically

## Endpoints

### `GET /companies` — Search & list

Search over **1 million Polish companies** with combined AND filters. Returns paginated results
with financial metrics per row. Supports both offset and cursor-based pagination.

```bash
# Top 10 manufacturing companies in Mazovia with revenue > 10M PLN
curl "https://compabase.com/api/v1/companies?pkd=28&voivodeship=mazowieckie&revenue_min=10000000&limit=10" \
  -H "X-API-Key: YOUR_API_KEY"
```

**Pagination modes**

| Mode | Parameters | Best for |
|------|-----------|----------|
| Offset | `offset`, `limit` | Interactive browsing, UI tables |
| Cursor | `cursor`, `limit` | Deep export, large dataset traversal |

**Available filters**

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Full-text name search |
| `keywords` | string | Keyword token search |
| `pkd` | string | PKD industry code (e.g. `46.90.Z`); combine `primary_only=true` to match primary activity only |
| `voivodeship` | string | Polish region / voivodeship (lowercase, e.g. `mazowieckie`) |
| `city` | string | City name (uppercase, e.g. `WARSZAWA`) |
| `postal_code` | string | Postal code prefix |
| `legal_form` | string | Legal form (e.g. `SPÓŁKA AKCYJNA`, `SPÓŁKA Z OGRANICZONĄ ODPOWIEDZIALNOŚCIĄ`) |
| `status_active` | bool | `true` = active only, `false` = inactive only |
| `has_email` | bool | Filter by email presence |
| `has_website` | bool | Filter by website presence |
| `currency` | string | Currency for financial filter thresholds (`PLN`, `USD`, `EUR`) |
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
| `registration_year_min` / `registration_year_max` | integer | Company registration year range |
| `last_report_year_min` / `last_report_year_max` | integer | Last financial report year range |

---

### `GET /companies/count` — Count matching companies

Returns a single `count` integer for the same filter set as `/companies` (no pagination params).
Useful for building faceted search UIs, dashboards, or estimating export sizes before fetching pages.

```bash
curl "https://compabase.com/api/v1/companies/count?pkd=46.90.Z&voivodeship=śląskie" \
  -H "X-API-Key: YOUR_API_KEY"
# → { "count": 3142 }
```

---

### `GET /companies/krs/{krs}` — Full company profile

Returns the complete profile for a Polish company identified by its **KRS number**
(Polish National Court Register number). Leading zeros are optional — the API normalizes
the value to 10 digits server-side.

The response includes:
- `company` — core identifiers, address, PKD activities, display fields
- `details` — contacts, suspension/deletion flags, filings
- `financials` — multi-year financial metrics (PLN / USD / EUR)
- `roles` — management board members and other roles
- `ownership` — shareholder structure with holding percentages and values
- `companySummary` — short AI-generated description (PL / EN / DE)
- `similarCompanies` — related companies by industry and size

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /companies/krs/{krs}/financial-statements` — Financial statements

Financial metrics only, without profile metadata. Returns all available P&L, balance sheet,
and ratio columns grouped by reporting year — in PLN, USD and EUR.

Includes: `revenue_total`, `revenue_operating`, `profit_net`, `profit_gross`, `ebit`, `ebitda`,
`operating_costs_total`, `financial_income`, `financial_costs`, `income_tax`, `total_assets`,
`fixed_assets`, `current_assets`, `equity`, `liabilities_and_provisions`, `roa`, `net_margin`,
`asset_turnover`, and their USD/EUR equivalents.

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860/financial-statements" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /companies/krs/{krs}/structure-people` — Ownership & management

Returns `roles` (management board, supervisory board, proxies) and `ownership` (shareholders
with holding percentage, share count, and PLN / USD / EUR values) without any profile or
financial data.

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860/structure-people" \
  -H "X-API-Key: YOUR_API_KEY"
```

---

### `GET /companies/krs/{krs}/status` — Legal status

Lightweight status check — returns `status` (`active`, `bankruptcy`, `liquidation`) and
`is_open` flag. Useful for validating KRS numbers or monitoring status changes without
pulling the full profile.

```bash
curl "https://compabase.com/api/v1/companies/krs/0000028860/status" \
  -H "X-API-Key: YOUR_API_KEY"
# → { "krs": "0000028860", "status": "active", "label": "Active", "is_open": true }
```

---

### `GET /usage` — Quota usage

Returns current-month usage for the authenticated key. Does **not** consume quota.

```bash
curl "https://compabase.com/api/v1/usage" \
  -H "X-API-Key: YOUR_API_KEY"
# → { "used": 42, "limit": 5000, "remaining": 4958, "monthUtc": "2026-06", "resetsAtUtc": "2026-07-01T00:00:00Z" }
```

---

### `GET /health` — API health

Public endpoint (no API key required). Returns `{ "status": "ok", "api": "v1", "timestamp": "..." }`.

---

## Use cases

- Build ranked lists of companies by revenue, profitability, or assets within a specific Polish industry
- Identify companies in a given voivodeship with defined financial thresholds (revenue > X, EBITDA > Y)
- Retrieve full company profiles from KRS numbers collected via external sources (forms, databases, exports)
- Analyze multi-year financial performance with normalized PLN / USD / EUR metrics
- Track company lifecycle states (active, liquidation, bankruptcy) and detect status changes
- Explore ownership structures and management roles for due diligence or research
- Build datasets for market analysis (e.g. top companies in logistics, energy, manufacturing)
- Filter companies that have a website or email for lead generation or B2B enrichment
- Validate KRS numbers and check their current legal status in bulk

## OpenAPI Specification

This repository contains the official OpenAPI definition of the Compabase API.

- `v1.yaml` — source of truth for the API contract (OpenAPI 3.1)

## Base URL

```
https://compabase.com/api/v1/
```

## Documentation

Full interactive documentation (Redoc):
https://compabase.com/docs/

## Authentication

Most endpoints require an API key.

Supported headers:

```
X-API-Key: <your_key>
Authorization: Bearer <your_key>
```

## Quota & Plans

Each API key has a monthly request limit that resets on the 1st of each month (UTC).

| Plan | Requests / month |
|------|-----------------|
| Free | 100 |
| Pro | 5 000 |
| Scale | 100 000 |
| Enterprise | Custom |

- `GET /usage` does **not** consume quota
- `401` responses (auth failure before processing) do **not** consume quota
- `429` responses (quota exhausted) do **not** consume additional quota
- All other authenticated responses (`200`, `400`, `404`) **do** consume quota

## Access

Create a free account and generate your API keys instantly:
https://compabase.com/api-keys

## License

MIT
