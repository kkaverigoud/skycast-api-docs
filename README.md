# Skycast API Reference

**Version:** 1.0.0
**Last Updated:** February 2026
**Status:** Active

---

> **Portfolio Note:** This is a mock REST API reference created for a technical writing portfolio by **Kaveri Goud**. The Skycast API is a fictional product and is not live. This document is modelled on ServiceNow Developer Portal standards and authored entirely in Markdown.
> 📧 kkaverigoud138@gmail.com

---

## Table of Contents

1. [Overview](#overview)
2. [Base URL](#base-url)
3. [Authentication](#authentication)
4. [Rate Limits](#rate-limits)
5. [Endpoints](#endpoints)
   - [GET /current](#get-current)
   - [GET /forecast](#get-forecast)
   - [GET /historical](#get-historical)
   - [GET /alerts](#get-alerts)
6. [Error Codes](#error-codes)
7. [Changelog](#changelog)

---

## Overview

The **Skycast API** gives you real-time weather data, multi-day forecasts, historical records, and severe weather alerts — for any location in the world.

Whether you're building a travel app, a logistics dashboard, or a weather widget, Skycast lets you display accurate, live weather data without building your own weather infrastructure. All responses are returned in JSON.

**What you can do with the Skycast API:**

- Show real-time weather conditions in your app without managing data pipelines
- Plan ahead with day-by-day forecasts covering up to 7 days
- Analyse past weather patterns using up to 30 days of historical data
- Keep users safe by surfacing active severe weather alerts for their region

---

## Base URL

Every request you make to the Skycast API starts with this base URL:
All endpoints in this reference are relative to this base URL.

---

## Authentication

The Skycast API uses **API key authentication**. Every request must include your API key in the request header — requests made without a valid key will return a `401 Unauthorized` error.

### Get your API key

1. Sign up for a free account at skycast.io/signup
2. Go to **Dashboard → API Keys**
3. Click **Generate New Key**
4. Copy your key and store it somewhere safe — you won't be able to see it again

### Add your key to requests

Pass your API key in the `X-API-Key` header with every request:
**Example:**
```http
GET /v1/current?city=Hyderabad HTTP/1.1
Host: api.skycast.io
X-API-Key: YOUR_API_KEY_HERE
```

> **Keep your key private.** Never include it directly in client-side code or commit it to a public repository. Store keys in environment variables instead.

---

## Rate Limits

Your request limit depends on your plan:

| Plan     | Requests per Minute | Requests per Day |
|----------|---------------------|------------------|
| Free     | 10                  | 500              |
| Standard | 60                  | 10,000           |
| Pro      | 300                 | 100,000          |

If you go over your limit, you'll receive a `429 Too Many Requests` response. Check the `Retry-After` header in the response — it tells you exactly how many seconds to wait before trying again.

---

## Endpoints

All Skycast API endpoints use the **HTTP GET** method. The API is read-only and does not support POST, PUT, or DELETE operations. All responses are returned in JSON format.

---

### GET /current

Get the current weather conditions for any city or coordinates.
**Query Parameters:**

| Parameter | Type   | Required | Description                            |
|-----------|--------|----------|----------------------------------------|
| `city`    | string | Yes*     | City name (e.g., `Hyderabad`)          |
| `lat`     | float  | Yes*     | Latitude coordinate (e.g., `17.3850`)  |
| `lon`     | float  | Yes*     | Longitude coordinate (e.g., `78.4867`) |
| `units`   | string | No       | `metric` (default) or `imperial`       |

> *You need either `city` **or** `lat`/`lon` — not both. If you send both, coordinates take priority.

**Example Request:**
```http
GET /v1/current?city=Hyderabad&units=metric HTTP/1.1
Host: api.skycast.io
X-API-Key: YOUR_API_KEY_HERE
```

**Example Response:**
```json
{
  "status": "success",
  "location": {
    "city": "Hyderabad",
    "country": "IN",
    "lat": 17.3850,
    "lon": 78.4867
  },
  "current": {
    "temperature": 32,
    "feels_like": 36,
    "humidity": 58,
    "wind_speed": 12,
    "wind_direction": "SW",
    "condition": "Partly Cloudy",
    "condition_code": 802,
    "visibility_km": 9,
    "uv_index": 7,
    "timestamp": "2026-02-25T14:00:00Z"
  },
  "units": "metric"
}
```

**Response Fields:**

| Field            | Type    | Description                                                                 |
|------------------|---------|-----------------------------------------------------------------------------|
| `temperature`    | integer | Current temperature in the unit specified (`metric` = °C, `imperial` = °F) |
| `feels_like`     | integer | Perceived temperature, accounting for humidity and wind chill               |
| `humidity`       | integer | Relative humidity as a percentage (0–100)                                   |
| `wind_speed`     | integer | Wind speed in km/h (metric) or mph (imperial)                               |
| `wind_direction` | string  | Cardinal wind direction (e.g., `N`, `SW`, `NE`)                            |
| `condition`      | string  | Plain-language weather description (e.g., `Partly Cloudy`)                 |
| `condition_code` | integer | Numeric weather condition code (e.g., `800` = Clear, `500` = Light Rain)   |
| `visibility_km`  | integer | Horizontal visibility in kilometres                                         |
| `uv_index`       | integer | UV index on a scale of 0 (Low) to 11+ (Extreme)                            |
| `timestamp`      | string  | Date and time of the reading in ISO 8601 format (UTC)                      |

---

### GET /forecast

Get a day-by-day weather forecast for up to 7 days.
**Query Parameters:**

| Parameter | Type    | Required | Description                             |
|-----------|---------|----------|-----------------------------------------|
| `city`    | string  | Yes*     | City name                               |
| `lat`     | float   | Yes*     | Latitude coordinate                     |
| `lon`     | float   | Yes*     | Longitude coordinate                    |
| `days`    | integer | No       | Number of days: `1`–`7` (default: `3`)  |
| `units`   | string  | No       | `metric` (default) or `imperial`        |

**Example Request:**
```http
GET /v1/forecast?city=Mumbai&days=3&units=metric HTTP/1.1
Host: api.skycast.io
X-API-Key: YOUR_API_KEY_HERE
```

**Example Response:**
```json
{
  "status": "success",
  "location": {
    "city": "Mumbai",
    "country": "IN"
  },
  "forecast": [
    {
      "date": "2026-02-26",
      "temp_high": 34,
      "temp_low": 24,
      "humidity": 72,
      "condition": "Sunny",
      "condition_code": 800,
      "rain_probability": 5
    },
    {
      "date": "2026-02-27",
      "temp_high": 32,
      "temp_low": 23,
      "humidity": 78,
      "condition": "Partly Cloudy",
      "condition_code": 802,
      "rain_probability": 25
    },
    {
      "date": "2026-02-28",
      "temp_high": 30,
      "temp_low": 22,
      "humidity": 85,
      "condition": "Light Rain",
      "condition_code": 500,
      "rain_probability": 75
    }
  ],
  "units": "metric"
}
```

**Response Fields:**

| Field              | Type    | Description                                                |
|--------------------|---------|------------------------------------------------------------|
| `date`             | string  | Forecast date in `YYYY-MM-DD` format                       |
| `temp_high`        | integer | Predicted maximum temperature for the day                  |
| `temp_low`         | integer | Predicted minimum temperature for the day                  |
| `humidity`         | integer | Expected average relative humidity as a percentage (0–100) |
| `condition`        | string  | Plain-language weather description for the day             |
| `condition_code`   | integer | Numeric weather condition code                             |
| `rain_probability` | integer | Likelihood of rainfall as a percentage (0–100)             |

---

### GET /historical

Look up weather data for any date in the past 30 days.
**Query Parameters:**

| Parameter    | Type   | Required | Description                        |
|--------------|--------|----------|------------------------------------|
| `city`       | string | Yes*     | City name                          |
| `lat`        | float  | Yes*     | Latitude coordinate                |
| `lon`        | float  | Yes*     | Longitude coordinate               |
| `start_date` | string | Yes      | Start date in `YYYY-MM-DD` format  |
| `end_date`   | string | Yes      | End date in `YYYY-MM-DD` format    |
| `units`      | string | No       | `metric` (default) or `imperial`   |

> Dates must fall within the last 30 days. Requests for older dates return a `400 Bad Request` error.

**Example Request:**
```http
GET /v1/historical?city=Delhi&start_date=2026-02-01&end_date=2026-02-03 HTTP/1.1
Host: api.skycast.io
X-API-Key: YOUR_API_KEY_HERE
```

**Example Response:**
```json
{
  "status": "success",
  "location": {
    "city": "Delhi",
    "country": "IN"
  },
  "historical": [
    {
      "date": "2026-02-01",
      "temp_avg": 18,
      "temp_high": 23,
      "temp_low": 13,
      "humidity": 60,
      "condition": "Haze",
      "condition_code": 721
    },
    {
      "date": "2026-02-02",
      "temp_avg": 19,
      "temp_high": 24,
      "temp_low": 14,
      "humidity": 58,
      "condition": "Clear",
      "condition_code": 800
    },
    {
      "date": "2026-02-03",
      "temp_avg": 17,
      "temp_high": 21,
      "temp_low": 12,
      "humidity": 70,
      "condition": "Overcast",
      "condition_code": 804
    }
  ],
  "units": "metric"
}
```

**Response Fields:**

| Field            | Type    | Description                                              |
|------------------|---------|----------------------------------------------------------|
| `date`           | string  | The historical date in `YYYY-MM-DD` format               |
| `temp_avg`       | integer | Average temperature recorded for the day                 |
| `temp_high`      | integer | Highest temperature recorded for the day                 |
| `temp_low`       | integer | Lowest temperature recorded for the day                  |
| `humidity`       | integer | Average relative humidity for the day as a percentage    |
| `condition`      | string  | Plain-language description of the day's dominant weather |
| `condition_code` | integer | Numeric weather condition code                           |

---

### GET /alerts

Check for active severe weather alerts in a location. Returns an empty array if no alerts are active — so you can always safely check without worrying about null responses.
**Query Parameters:**

| Parameter | Type   | Required | Description          |
|-----------|--------|----------|----------------------|
| `city`    | string | Yes*     | City name            |
| `lat`     | float  | Yes*     | Latitude coordinate  |
| `lon`     | float  | Yes*     | Longitude coordinate |

**Example Request:**
```http
GET /v1/alerts?city=Chennai HTTP/1.1
Host: api.skycast.io
X-API-Key: YOUR_API_KEY_HERE
```

**Example Response — active alert:**
```json
{
  "status": "success",
  "location": {
    "city": "Chennai",
    "country": "IN"
  },
  "alerts": [
    {
      "alert_id": "ALT-20260225-001",
      "type": "Cyclone Warning",
      "severity": "High",
      "headline": "Cyclonic storm expected to make landfall within 48 hours",
      "issued_by": "India Meteorological Department",
      "effective_from": "2026-02-25T18:00:00Z",
      "expires_at": "2026-02-27T18:00:00Z",
      "areas_affected": ["Chennai", "Puducherry", "Cuddalore"]
    }
  ]
}
```

**Example Response — no active alerts:**
```json
{
  "status": "success",
  "location": {
    "city": "Chennai",
    "country": "IN"
  },
  "alerts": []
}
```

**Response Fields:**

| Field            | Type             | Description                                                  |
|------------------|------------------|--------------------------------------------------------------|
| `alert_id`       | string           | Unique identifier for the alert (e.g., `ALT-20260225-001`)   |
| `type`           | string           | Category of the alert (e.g., `Cyclone Warning`, `Heat Wave`) |
| `severity`       | string           | Alert severity: `Low`, `Moderate`, `High`, or `Extreme`      |
| `headline`       | string           | Short plain-language summary of the alert                    |
| `issued_by`      | string           | Name of the authority that issued the alert                  |
| `effective_from` | string           | Date and time the alert becomes active (ISO 8601, UTC)       |
| `expires_at`     | string           | Date and time the alert expires (ISO 8601, UTC)              |
| `areas_affected` | array of strings | List of cities or regions covered by the alert               |

---

## Error Codes

When something goes wrong, the Skycast API always returns a consistent error format so you know exactly what happened and what to do next.

**Error response format:**
```json
{
  "status": "error",
  "error": {
    "code": 401,
    "type": "UNAUTHORIZED",
    "message": "Invalid or missing API key."
  }
}
```

**Error reference:**

| HTTP Code | Type                  | What it means                                                     |
|-----------|-----------------------|-------------------------------------------------------------------|
| `400`     | `BAD_REQUEST`         | A required parameter is missing or the date range is invalid      |
| `401`     | `UNAUTHORIZED`        | Your API key is missing, incorrect, or has expired                |
| `403`     | `FORBIDDEN`           | This endpoint isn't available on your current plan                |
| `404`     | `NOT_FOUND`           | The city or location you specified couldn't be found              |
| `429`     | `RATE_LIMIT_EXCEEDED` | You've hit your plan's request limit — check `Retry-After` header |
| `500`     | `INTERNAL_ERROR`      | Something went wrong on our end. Wait a moment and try again      |

---

## Changelog

| Version | Date          | What changed                                                             |
|---------|---------------|--------------------------------------------------------------------------|
| 1.1.0   | March 2026    | Added `lang` parameter to /current and /forecast for localised responses |
| 1.0.1   | February 2026 | Fixed missing `temp_avg` field in /historical response schema            |
| 1.0.0   | February 2026 | Initial release — /current, /forecast, /historical, /alerts              |

---

*Questions or feedback? Reach out at api-support@skycast.io*

---

*Documentation authored by [Kaveri Goud](mailto:kkaverigoud138@gmail.com)*
