# Case Study — Multi-City Weather Monitoring with Dashboard

## The client (illustrative example)

**Company:** FreshRoute Logistics
**Industry:** Refrigerated food distribution
**Size:** 30 employees
**Contact:** Marcela Díaz, operations manager

## The problem

FreshRoute delivers temperature-sensitive products across three major cities. Weather directly affects their operations: heavy rain delays routes, and high temperatures increase the risk to refrigerated cargo. Their team checked the weather manually each morning, city by city, with no consolidated view and no historical record to spot patterns.

The pain points:

- Manual, repetitive weather checks across multiple sources.
- No single place to compare conditions across all three cities.
- No historical data to analyze how weather correlated with delivery delays.
- Reactive instead of proactive planning.

## The proposed solution

An automation that fetches live weather for all three cities on a schedule, logs it to a historical spreadsheet, and presents it in a dashboard:

1. **Automatic fetch** of temperature, feels-like, humidity, and conditions for the three cities.
2. **Historical log** in Google Sheets, building a time series automatically.
3. **Visual dashboard** to compare cities at a glance.

The team now opens one dashboard instead of checking three weather sites, and accumulates data they can later cross-reference with delivery performance.

## Implementation

Built in n8n with a Schedule Trigger, a Code node defining the three cities, an HTTP Request node calling the OpenWeatherMap API, and a Google Sheets node logging each reading. A dashboard tab with charts visualizes the data, and a standalone HTML page provides a polished snapshot view.

The key technical step was consuming the public API directly with the HTTP Request node and handling the nested response structure (temperature inside `main`, conditions inside a `weather` list).

## Expected results

| Metric | Before | After |
|--------|--------|-------|
| Daily weather checks | Manual, 3 sources | Automatic, 1 dashboard |
| Historical record | None | Continuous time series |
| Cross-city comparison | Not possible | At a glance |
| Planning style | Reactive | Proactive |

## Project value

A multi-location data-collection automation with a dashboard is worth an estimated **$300–600 USD** on freelance platforms. Its appeal is twofold: it removes a daily manual task and creates a data asset (the historical log) that grows in value over time.

## Key technical takeaway

This project taught how to consume any public API directly via the HTTP Request node — a skill that generalizes far beyond weather, since most services expose a REST API. It also reinforced handling nested API responses and processing multiple items (cities) in a single workflow run.

---

*Note: This case study is illustrative, for portfolio purposes. Client data is fictional.*
