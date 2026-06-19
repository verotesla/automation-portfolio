# Project 7: Weather API → Google Sheets + Dashboard

![Status](https://img.shields.io/badge/Status-Done-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Time](https://img.shields.io/badge/Time-3%20hours-blue)
![Tools](https://img.shields.io/badge/Tools-n8n%20%7C%20OpenWeatherMap%20%7C%20Google%20Sheets-purple)

🌐 **English** | [Español](./README.es.md)

## Description

An automation that fetches live weather data for three Mexican cities (Mexico City, Guadalajara, Monterrey) from a public API, logs it to a historical Google Sheet, and visualizes it in a dashboard. It runs on a schedule, so the weather history builds up automatically over time.

This project introduces a key skill: **consuming a public API directly** with the HTTP Request node, without relying on a pre-built integration. That opens the door to thousands of services.

## Use cases

- **Operations planning:** businesses affected by weather (logistics, agriculture, events) tracking conditions across locations.
- **Multi-location monitoring:** comparing metrics across several cities or sites in one place.
- **Historical data collection:** building a time series from any public API for later analysis.
- **Automated reporting:** feeding a live dashboard without manual data entry.

## Tech stack

| Component | Tool | Role |
|-----------|------|------|
| Trigger | Schedule Trigger | Runs the fetch periodically |
| City list | Code node | Defines the 3 cities as separate items |
| API call | HTTP Request | Fetches weather from OpenWeatherMap |
| Storage | Google Sheets | Logs the historical record |
| Visualization | Sheets charts + HTML dashboard | Displays the data |

## Workflow architecture

```
┌──────────┐   ┌────────────┐   ┌──────────────┐   ┌──────────────────┐
│ Schedule │──▶│ Code       │──▶│ HTTP Request │──▶│ Google Sheets    │
│ Trigger  │   │ (3 cities) │   │ (Weather API)│   │ (Append Row)     │
└──────────┘   └────────────┘   └──────────────┘   └──────────────────┘
                  3 items          3 results          3 rows logged
```

## Key concept: the HTTP Request node

Unlike pre-built nodes (Slack, Airtable), the **HTTP Request** node calls any public API. This is one of n8n's most powerful tools, because it removes the dependency on having a dedicated integration.

The call to OpenWeatherMap uses query parameters:

| Parameter | Value | Purpose |
|-----------|-------|---------|
| `q` | `{{ $json.ciudad }}` | City to query (one per item) |
| `appid` | API key | Authentication |
| `units` | `metric` | Returns temperature in Celsius |

## Processing multiple items

The Code node returns three items (one per city). n8n then processes all three automatically through the following nodes, so a single run fetches and logs all three cities at once:

```javascript
return [
  { json: { ciudad: "Mexico City", nombre: "CDMX" } },
  { json: { ciudad: "Guadalajara", nombre: "Guadalajara" } },
  { json: { ciudad: "Monterrey", nombre: "Monterrey" } }
];
```

## Data logged to Google Sheets

| Column | Source |
|--------|--------|
| Date | `{{ $now.toFormat('dd/MM/yyyy HH:mm:ss') }}` |
| City | `{{ $json.name }}` |
| Temperature | `{{ $json.main.temp }}` |
| Feels like | `{{ $json.main.feels_like }}` |
| Humidity | `{{ $json.main.humidity }}` |
| Description | `{{ $json.weather[0].description }}` |

> Note: `weather` is a list, so `[0]` takes the first element. `main` is a nested object holding the temperature values.

## Visualization

Two complementary dashboards:

1. **Google Sheets charts (live):** bar charts for temperature and humidity by city, plus a temperature-vs-feels-like comparison. These update automatically as new rows are added.
2. **HTML dashboard (static showcase):** a styled page with color-coded city cards (blue = cold, amber = mild, red = warm). A portfolio showcase piece built from the first run's data.

## Concepts learned

- **HTTP Request node:** consuming any public API directly with query parameters.
- **API authentication** via key as a query parameter (no OAuth needed).
- **Processing multiple items** in a single workflow run.
- **Accessing nested and list data** (`main.temp`, `weather[0].description`).
- **Unit handling** (`units=metric` for Celsius).
- Building **dashboards** in Google Sheets and as a standalone HTML page.

## How to implement

See [`setup-guide.md`](./setup-guide.md) for the full step-by-step guide.

## Freelance value

A multi-location data-collection automation with a dashboard is worth an estimated **$300–600 USD** on freelance platforms. The HTTP Request skill is especially valuable because it generalizes to any API a client might need.

## Screenshots

See the [`/assets`](./assets) folder for screenshots of the workflow and dashboards.

## Contact

**Veronica Pacheco**
GitHub: [@verotesla](https://github.com/verotesla)
