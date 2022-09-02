# Locations

## Definitions

### Location

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`|int|the unique identifier|
| `type`|string|the type of location (`market`, `port`, or `zip3`)|
| `geographyType`|string|the type of geography (`circle` or `none`)|
| `date`|timestamp|time and date of location creation|
| `name`|string|the name of the location|
| `state`|string|the state or province of the location, if applicable|
| `countryId`|int|the unique identifier of the country
| `country`|string|the name of the country|
| `countryCode`|string|the country code in ISO-3166-1-alpha-2 format|
| `display`|string|display name in format: Country - Name, State|

#### Note(s)
1. Market and port locations are used to create international moves and zip3s are used for CONUS moves.

## Routes

### Get Locations

Request:
```
GET /api/v2/locations
Content-Type: application/json
x-api-key: {{ api-key }}
```
Response:
```
HTTP/1.1 200 OK

{
    "data": [
        {
            "type": "market",
            "geographyType": "circle",
            "date": "2022-08-31T18:03:32.000Z",
            "name": "Melbourne",
            "countryId": 4,
            "state": "Vic",
            "id": 1864,
            "country": "Australia",
            "countryCode": "AU",
            "display": "Australia - Melbourne, Vic"
        },
        {
            "type": "market",
            "geographyType": "circle",
            "date": "2022-08-31T18:03:32.000Z",
            "name": "Perth",
            "countryId": 4,
            "state": "WA",
            "id": 1865,
            "country": "Australia",
            "countryCode": "AU",
            "display": "Australia - Perth, WA"
        },
        ...
    ],
    "limit": 50,
    "offset": 0,
    "length": 50
}
```

### Search Locations
This route is used for finding PricePoint locations within ~100 miles of the provided coordinates. It is also used to find locations with a given zip3.

International EX:   
Request:
```
POST /api/v1/locationsSearch
Content-Type: application/json
x-api-key: {{ api-key }}

{
    "latitude": -37.8409,
    "longitude": 144.9464,
    "countryCode": "AU"
}
```
Response:
```
HTTP/1.1 200 OK

{
    "latitude": -37.8409,
    "longitude": 144.9464,
    "countryCode": "AU",
    "locations": {
        "data": [
            {
                "type": "market",
                "countryCode": "AU",
                "geography": {
                    "locationId": 1864,
                    "latitude": -37.8136,
                    "longitude": 144.963,
                    "radius": 47,
                    "id": 654
                },
                "geographyType": "circle",
                "date": "2022-08-31T18:03:32.000Z",
                "name": "Melbourne",
                "countryId": 4,
                "state": "Vic",
                "id": 1864,
                "country": "Australia",
                "display": "Australia - Melbourne, Vic",
                "mileage": 2.0946423151999998,
                "extraMileage": 0
            }
        ],
        "limit": 1,
        "offset": 0,
        "length": 1
    }
}
```

CONUS EX:   
Request:
```
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: {{ api-key }}

{
    "type": "zip3",
    "zip3": "021"
}
```
Response:
```
HTTP/1.1 200 OK

{
    "latitude": 42.3611,
    "longitude": -71.057,
    "countryCode": "US",
    "zip3": "021",
    "locations": {
        "data": [
            {
                "type": "zip3",
                "countryCode": "US",
                "geography": null,
                "geographyType": "circle",
                "date": "2022-08-29T20:13:01.000Z",
                "name": "021 - Boston, MA",
                "countryId": 3,
                "state": "MA",
                "id": 4414,
                "country": "USA",
                "display": "021 - Boston, MA",
                "serviceArea": 385,
                "city": "Boston",
                "zip3": "021",
                "locationId": 4414,
                "mileage": 0,
                "extraMileage": 0
            },
            ...
        ],
        "limit": 11,
        "offset": 0,
        "length": 11
    }
}
```
#### Note(s)
1. The mileage displays the exact distance between the input coordinates and the locations coordinates, while the extraMileage displays the distance from the input coordinates to the outermost coordinate considered part of the resulting location.
1. You must always provide values for latitude, longitude,
and countryCode.