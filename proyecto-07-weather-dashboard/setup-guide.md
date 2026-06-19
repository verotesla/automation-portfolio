# Setup Guide — Project 7: Weather API → Sheets + Dashboard

This guide walks through building a workflow that fetches live weather for three cities, logs it to Google Sheets, and visualizes it.

---

## Prerequisites

- [n8n.cloud](https://n8n.cloud) account
- Free [OpenWeatherMap](https://openweathermap.org) account (for the API key)
- Google account (Sheets)
- Estimated time: 2–3 hours

---

## Part 1 — Get the OpenWeatherMap API key

1. Sign up at [openweathermap.org](https://openweathermap.org).
2. Confirm your email.
3. Go to **My API keys** (or [home.openweathermap.org/api_keys](https://home.openweathermap.org/api_keys)).
4. Copy the API key.

> ⚠️ The key can take a few minutes to a couple of hours to activate. If you get "invalid API key", wait and retry.

---

## Part 2 — Schedule Trigger

1. Create a workflow named `Weather Dashboard`.
2. Add a **Schedule Trigger** (every 1–3 hours for production; any interval for testing).

---

## Part 3 — Define the cities (Code node)

1. From the Schedule Trigger, add a **Code** node.
2. Paste:

```javascript
return [
  { json: { ciudad: "Mexico City", nombre: "CDMX" } },
  { json: { ciudad: "Guadalajara", nombre: "Guadalajara" } },
  { json: { ciudad: "Monterrey", nombre: "Monterrey" } }
];
```

3. Execute the node — it should return 3 items.

> The `ciudad` field is the API-friendly city name; `nombre` is the display label.

---

## Part 4 — HTTP Request (the weather API call)

1. From the Code node, add an **HTTP Request** node.
2. **Method:** GET.
3. **URL:** `https://api.openweathermap.org/data/2.5/weather`
4. **Authentication:** None (the API key goes as a query parameter).
5. Enable **Send Query Parameters** and add:

| Name | Value |
|------|-------|
| `q` | `{{ $json.ciudad }}` |
| `appid` | YOUR_API_KEY |
| `units` | `metric` |

6. Execute — it should return weather data for the 3 cities.

> ⚠️ "invalid API key" almost always means the key isn't active yet. Wait and retry.

---

## Part 5 — Create the Google Sheet

Create a sheet `Clima Histórico` with headers:

| A | B | C | D | E | F |
|---|---|---|---|---|---|
| Date | City | Temperature | Feels like | Humidity | Description |

---

## Part 6 — Google Sheets (Append Row)

1. From the HTTP Request, add **Google Sheets → Append Row**.
2. Select the document and sheet.
3. Map the fields:

| Column | Expression |
|--------|-----------|
| Date | `{{ $now.toFormat('dd/MM/yyyy HH:mm:ss') }}` |
| City | `{{ $json.name }}` |
| Temperature | `{{ $json.main.temp }}` |
| Feels like | `{{ $json.main.feels_like }}` |
| Humidity | `{{ $json.main.humidity }}` |
| Description | `{{ $json.weather[0].description }}` |

4. Execute — it should create 3 rows (one per city) in a single run.

---

## Part 7 — Dashboard in Google Sheets

1. Add a new tab named `Dashboard`.
2. **Temperature by city:** select City + Temperature columns → Insert → Chart → Column chart.
3. **Humidity by city:** select City + Humidity (Ctrl to select non-adjacent columns) → Insert → Chart.
4. **Temperature vs feels like:** select City + Temperature + Feels like → Insert → Chart → Column.
5. Move all charts to the `Dashboard` tab.

> Charts auto-update as the workflow adds new rows.

---

## Part 8 — Publish

1. Publish/activate the workflow so it runs on schedule.
2. The historical sheet and charts will grow automatically over time.

---

## Errors and fixes

| Error / Situation | Cause | Fix |
|-------------------|-------|-----|
| `invalid API key` | Key not active yet | Wait a few minutes/hours and retry |
| Temperature in Kelvin | Missing `units` param | Add `units=metric` |
| Only 1 city processed | City list not returning items | Check the Code node returns 3 items |
| `weather` is undefined | Wrong nesting | Use `weather[0].description` (it's a list) |

---

## Possible improvements

- Add more weather fields (wind, pressure, sunrise/sunset).
- Alert (Slack/email) when temperature crosses a threshold.
- Add more cities by extending the Code node list.
- Connect the HTML dashboard to live data via a backend (keeping the API key server-side).
