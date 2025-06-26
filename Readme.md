# Old School RuneScape Real-Time Price API

![Logo](https://private-user-images.githubusercontent.com/94769460/459490990-652fde1a-e07f-4c62-88be-69343bf094be.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NTA5NTA1NDMsIm5iZiI6MTc1MDk1MDI0MywicGF0aCI6Ii85NDc2OTQ2MC80NTk0OTA5OTAtNjUyZmRlMWEtZTA3Zi00YzYyLTg4YmUtNjkzNDNiZjA5NGJlLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNTA2MjYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjUwNjI2VDE1MDQwM1omWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPTI1NzRjYjk3Mzc5NGRmYzhjYTc0YjhmOGZkODIzN2NlOGY5Y2JhODA5NmZlZGQ3YjBmNGM2OGE0MmRlYmM2NTcmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0In0.ataIFDVwAwe1ZAhQIrKetG1LYjLGu598Y_DBcMxOHjc)

## Table of Contents

1. [Introduction](#introduction)  
2. [General Information](#general-information)  
  - [Base URL](#base-url)  
  - [Versions](#versions)  
  - [Authentication / User-Agent](#authentication--user-agent)  
  - [Acceptable Use](#acceptable-use)  
  - [Discord Channel](#discord-channel)  
3. [Endpoints](#endpoints)  
  - [Latest Price (all items)](#latest-price-all-items)  
  - [Mapping](#mapping)  
  - [5-Minute Prices](#5-minute-prices)  
  - [1-Hour Prices](#1-hour-prices)  
  - [Time Series](#time-series)  
4. [Deadman Mode (DMM)](#deadman-mode-dmm)  
5. [Usage Examples](#usage-examples)  
6. [Common Error Codes](#common-error-codes)  
7. [Contact and Support](#contact-and-support)   

---

## Introduction

The **Old School RuneScape (OSRS) Real-Time Price API** provides programmatic access to Grand Exchange price quotes, cached and supplied in collaboration with RuneLite. It allows you to:

- Query current (high/low) prices for all items.  
- Get averages in 5 min or 1 h blocks.  
- Extract historical series (up to 365 points).  

---

## General Information

### Base URL

- Standard OSRS:  
  `https://prices.runescape.wiki/api/v1/osrs`

- Deadman Mode (DMM):  
  `https://prices.runescape.wiki/api/v1/dmm`

### Versions

Currently available: **v1**. Future versions will be reflected in the path.

### Authentication / User-Agent

- No key or token required.  
- **Required:** Send a `User-Agent` header identifying your tool and contact (e.g. `my_tracker - @DiscordUser`).  
- Prior contact helps with notifications about changes or maintenance.

### Acceptable Use

- No explicit ratelimit, but sustained **multiple heavy queries per second** may block your access.  
- To get all items, use `/latest` without the `id` parameter in a loop.

### Discord Channel

Join `#api-discussion` in the official OSRS Wiki Discord for:  
- Announcements about changes and maintenance  
- Questions and integration coordination

---

## Endpoints

All endpoints use **GET** and return JSON.

### Latest Price (all items)

`GET /latest`

**Description:** Latest `high`/`low` prices + transaction timestamps for each item.

#### Query Parameters

| Parameter | Type    | Required | Description                                         |
|-----------|---------|----------|-----------------------------------------------------|
| id        | integer | No       | Filter a single item by ID (not recommended for all)|

#### Response

```json
{
  "data": {
   "2":    { "high": 2500,    "highTime": 1670000000,    "low": 2450,    "lowTime": 1670001000 },
   "4151": { "high": 1250000, "highTime": 1670000500,    "low": 1245000, "lowTime": 1670000800 }
   // ...
  }
}
```

### Mapping

`GET /mapping`

**Description:** Get a mapping from ID to item name.

#### Query Parameters

| Parameter | Type    | Required | Description                                         |
|-----------|---------|----------|-----------------------------------------------------|
| id        | integer | No       | Filter a single item by ID (not recommended for all)|

#### Response

```json
[
  {
   "id": 10344,
   "name": "3rd age amulet",
   "examine": "Fabulously ancient mage protection…",
   "members": true,
   "lowalch": 20200,
   "highalch": 30300,
   "value": 50500,
   "limit": 8,
   "icon": "3rd age amulet.png"
  }
  // ...
]
```

### 5-Minute Prices

`GET /prices/5m`

**Description:** 5-minute average prices for all items.

#### Query Parameters

| Parameter  | Type    | Required | Description                                                |
|------------|---------|----------|------------------------------------------------------------|
| timestamp  | integer | No       | Start of 5 min block (Unix), latest block if omitted.      |

#### Response

```json
{
  "data": [
   {
    "id": 2,
    "avgHighPrice": 2500,
    "avgLowPrice": 2450,
    "volume": 123,
    "timestamp": 1670000000
   }
   // ...
  ]
}
```

### 1-Hour Prices

`GET /prices/1h`

**Description:** 1-hour average prices for all items.

#### Response

```json
{
  "data": [
   {
    "id": 2,
    "avgHighPrice": 2550,
    "avgLowPrice": 2460,
    "volume": 500,
    "timestamp": 1669999200
   }
   // ...
  ]
}
```

### Time Series

`GET /timeseries`

**Description:** Historical series of `high` and `low` for an item, intervals of `5m`, `1h`, `6h`, or `24h`. Up to 365 points.

#### Query Parameters

| Parameter | Type    | Required | Description                                 |
|-----------|---------|----------|---------------------------------------------|
| id        | integer | Yes      | Item ID.                                    |
| timestep  | string  | Yes      | Interval: `"5m"`, `"1h"`, `"6h"`, `"24h"`.  |

#### Response

```json
{
  "data": [
   {
    "timestamp": 1750836900,
    "avgHighPrice": 6505082,
    "avgLowPrice": 6447007,
    "highPriceVolume": 3,
    "lowPriceVolume": 1
   }
   // ...
  ]
}
```

---

## Deadman Mode (DMM)

Endpoints (`/latest`, `/prices/5m`, `/prices/1h`, `/timeseries`, etc.) work identically to standard OSRS, but return Deadman Mode (DMM) data. Just use the DMM base URL:

```
https://prices.runescape.wiki/api/v1/dmm
```

---

## Usage Examples

### Example Query with curl

```bash
curl -H "User-Agent: my_tracker - @DiscordUser" \
  "https://prices.runescape.wiki/api/v1/osrs/latest"
```

---

## Common Error Codes

| Code | Description                                         |
|------|-----------------------------------------------------|
| 400  | Bad Request: invalid or missing parameters.         |
| 404  | Not Found: resource does not exist (ID out of range).|
| 429  | Too Many Requests: excessive frequency.             |
| 500  | Internal Server Error: temporary internal failure.  |

---

## Contact and Support

- Discord: `#api-discussion` channel in the OSRS Wiki.
- Official documentation: [RuneScape:Real-time_Prices](https://oldschool.runescape.wiki/w/RuneScape:Real-time_Prices)
- OSRS Wiki – License CC BY-NC-SA 3.0
