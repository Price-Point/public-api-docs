# Currencies

## Definitions

### Currency

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`|bigint|the unique identifier|
| `name`|string|the name of the currency|
| `symbol`|string|the 3 letter symbol of the currency. EX: USD|
| `priceInUsd`|number|the price of the currency in relation to USD. 1 unit of currency = `priceInUsd` USD|

## Routes

### Get Currencies

Request:
```
GET /api/v2/currencies
Content-Type: application/json
x-api-key: {{ api-key }}
```
Response:
```
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
