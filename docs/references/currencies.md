---
layout: default
title: Currencies
nav_order: 1
parent: References
---
# Currencies
{: .no_toc }

{: .note }
This reference includes all supported resources and completely documents all supported fields. Fields not mentioned in this document are subject to change without notice. All referenced resources will remain backwards compatible in their supported fields. 

## Table of Contents
{: .no_toc .text-delta }
1. TOC
{:toc}
## Currency Resource
MIRANDA TOOD FILL THIS OUT The currency resource is used to determine the exchange rate used to convert from tariffs from the currency for a move???

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`|number|the unique identifier|
| `name`|string|the name of the currency|
| `symbol`|string|the 3 letter symbol of the currency. EX: USD|
| `priceInUsd`|number|the price of the currency in relation to USD. 1 unit of currency = `priceInUsd` USD|

### Examples

#### List Currencies
{: .no_toc }
Request:
```json
GET /api/v2/currencies
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "limit": 50,
    "offset": 0,
    "length": 29,
    "data": [
        {
            "id": 1,
            "name": "US Dollars",
            "symbol": "USD",
            "priceInUsd": 1
        },
        {
            "id": 2,
            "name": "Euro",
            "symbol": "EUR",
            "priceInUsd": 1.219215
        },
        {
            "id": 3,
            "name": "British Pound",
            "symbol": "GBP",
            "priceInUsd": 1.417559
        },
        {
            "id": 4,
            "name": "Canadian Dollar",
            "symbol": "CAD",
            "priceInUsd": 0.827391
        },
        ...
    }
}
```
