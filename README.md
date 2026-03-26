# Compabase API

Search and analyze Polish companies with financial data, legal status,
and ownership – all in one API.

## Quick Example

``` bash
curl "https://compabase.com/api/v1/companies/krs/0000028860" \
  -H "X-API-Key: YOUR_API_KEY"
```

``` json
{
  "entity_id": "ad2ae683-ccbd-49c6-a85c-648024435cb5",
"company_name": "ORLEN SPÓŁKA AKCYJNA",
"company_name_display": "Orlen",
"nip": null,
"regon": null,
"registry_number": "0000028860",
"legal_form": "SPÓŁKA AKCYJNA",
"is_active": true,
"latest_snapshot_id": "1956bcf0-34f4-4ab1-98af-eddf75c0d40a",
"extracted_at": "2026-03-19T13:41:20.998732+00:00",
"state_date": null,
"postal_code": "09-411",
"city": "PŁOCK",
"region": "mazowieckie",
"revenue_total": 304668000000,
"revenue_total_usd": 76338762214.98372,
"profit_net": 1383000000,
"profit_net_usd": 346529691.80656475,
"operating_costs_total": 301809000000,
"operating_costs_total_usd": 75622400400.90202,
"total_assets": 255368000000,
"total_assets_usd": 63985968428.96517,
"fixed_assets": 186761000000,
"fixed_assets_usd": 46795539964.921074,
"current_assets": 68607000000,
"current_assets_usd": 17190428464.044098,
"equity": 146689000000,
"equity_usd": 36754948634.42746,
"liabilities_and_provisions": 108679000000,
"liabilities_and_provisions_usd": 27231019794.53771,
"has_email": true,
"has_website": true,
"full_address": "09-411 Płock, Poland",
"email": "zarzad@orlen.pl",
"website": "orlen.pl",
"logo_url": "https://emjsevwafjsrwhpdijje.supabase.co/storage/v1/object/public/company-logos/ad2ae683-ccbd-49c6-a85c-648024435cb5.ico",
"currency": "PLN",
"period_to": null
    }
  ],
  "pagination": {
    "limit": 1,
    "offset": 0,
    "hasMore": true
  }
}
```

## What you can do

-   Search and filter companies by location, industry (PKD), and
    financial metrics
-   Retrieve full company profiles
-   Access financial statements and key performance metrics
-   Check legal status (active, liquidation, bankruptcy)
-   Explore ownership and management structure

## Use cases

- Build ranked lists of companies by revenue, profitability, or assets within a specific industry
- Identify companies operating in a given region with defined financial thresholds (e.g. revenue > X, EBITDA > Y)  
- Retrieve full company profiles from KRS numbers collected from external sources (forms, databases, exports)  
- Analyze financial performance across companies using normalized financial metrics (PLN/USD/EUR)  
- Track company lifecycle states (active, liquidation, bankruptcy) and detect status changes  
- Explore ownership structures and management roles for due diligence or research  
- Create datasets for market analysis (e.g. top companies in logistics, energy, manufacturing)  
- Filter companies that have a website or email for further processing or classification  

## OpenAPI Specification

This repository contains the official OpenAPI definition of the
Compabase API.

-   `openapi.yaml` --- source of truth for the API contract

## Base URL

    https://compabase.com/api/v1/

## Documentation

Full interactive documentation:\
https://compabase.com/docs/

## Authentication

Most endpoints require an API key.

Supported headers: - `X-API-Key: <your_key>` -
`Authorization: Bearer <your_key>`

## Access

Request API access:\
https://compabase.com/api

## License

MIT
