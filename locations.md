# Locations

## Definitions

### Location
| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `id`        | bigint   | the unique identifier|
| `name`        | string   | the name of the location |
| `country`        |  string  | the country of the location |
| `type`        |  string  | the type of location (`market`, `port`, or `zip3`) |

#### Note(s)
1. Market locations are used to create international moves, and zip3s are used for CONUS moves

## Routes

### Get Locations

Request:
```
GET /api/v2/locations
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "data": [
        {
            "type": "market",
            "id": 1864,
            "name": "Melbourne",
            "country": "Australia"
        },
        {
            "type": "market",
            "id": 1865,
            "name": "Perth",
            "country": "Australia"
        },
        ...
    ],
    "limit": 50,
    "offset": 0,
    "length": 50
}
```

### Search Locations
This route is used for finding PricePoint locations within ~100 miles of the provided coordinates

Request:
```
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: api-key

{
    "latitude": -55.5555,
    "longitude": 55.5555
}
```
Response:
```
HTTP/1.1 200 OK

{
    "data": [
        {
            "type": "market",
            "id": 1864,
            "name": "Melbourne",
            "country": "Australia",
            "score": 80
        },
        {
            "type": "market",
            "id": 1865,
            "name": "Perth",
            "country": "Australia",
            "score": 30
        },
        ...
    ],
}
```
#### Note(s)
1. The response contains a list of locations sorted by a score. The higher the score, the closer that location is to the given coordinates.