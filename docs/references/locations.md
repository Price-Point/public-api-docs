---
layout: default
title: Locations
nav_order: 2
parent: References
---
# Locations
{: .no_toc }

{: .note }
This reference includes all supported resources and completely documents all supported fields. Fields not mentioned in this document are subject to change without notice. All referenced resources will remain backwards compatible in their supported fields. 

## Table of contents
{: .no_toc .text-delta }
1. TOC
{:toc}
## Location Resource
MIRANDA TODO DESCRIBE LOCATIONS The Locations API supports discovery of Location objects representing physical locations, often to be used as an origins and destinations for [Moves](corporate_accounts.html#move-resource). There are 3 supported types of locations.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
|`id`|number|the unique identifier|
|`type`|string|the type of location (`market`, `port`, or `zip3`)|
|`name`|string|the name of the location|
|`state`|string|the state or province of the location, if applicable|
|`country`|string|the name of the country|
|`countryCode`|string|the country code in ISO-3166-1 alpha-2 format|
|`display`|string|display name in format: Country - Name, State|
|`geography`|[Geography](#geography-resource)| the geographic area of the market|

### Market
MIRANDA TODO DESCRIBE MARKET A market locations represents an area of service for a mover. Markets are the most common types of locations, used as origins and destinations for all types of moves.
### Zip3
MIRANDA TODO DESCRIBE ZIP3
Includes all the properties of [Location Resource](#location-resource) and adds the following fields.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
|`zip3`|string|the first 3 digits of the US zip code|
|`city`|string|the name of the associated city|

### Port
MIRANDA TODO DESCRIBE PORT

### Examples
#### List Locations
{: .no_toc }
Request:
```json
GET /api/v2/locations
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "data": [
        {
            "type": "market",
            "name": "Melbourne",
            "state": "Vic",
            "id": 1,
            "country": "Australia",
            "countryCode": "AU",
            "display": "Australia - Melbourne, Vic"
        },
        {
            "type": "market",
            "name": "Perth",
            "state": "WA",
            "id": 2,
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
## Geography Resource
A geography sub resource describes the geographic space considered as part of the locations. Used for the [Search](#location-search) to determine which locations to return.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
|`id`|number|the unique identifier|
|`type`|string|the type of geography (`circle`)|
|`radius`|number|type distance from the longitude / latitude that the location covers|
|`longitude`|number|the longitude of the center of the geography|
|`latitude`|number|the latitude of the center of the geography|

### Examples
#### Read Geography
{: .no_toc }
Request:
```json
GET /api/v2/locations/1/geography
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "latitude": 41.85,
    "longitude": -8.86,
    "radius": 10,
    "type": "circle"
}
```

## Location Search
The locations resource also supports queries by geographic locations information. This endpoint will return all locations (enhanced to add mileage information) considered to be valid origins and destinations for [Moves](corporate_accounts.html#move-resource).

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
|`countryCode`|string|the country to be searched|
|`longitude`|number|the longitude of the search|
|`latitude`|number|the latitude of the search|
|`zip3`|string|(options) a zip3 to consider for CONUS moves|
|`locations`|[Location](#location-with-mileage)|the search results|

### Location With Mileage
Includes all the properties of [Location Resource](#location-resource) and adds the following fields.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
|`mileage`|number|the distance from the center of the location to the search location|
|`extraMileage`|number|the additional mileage that may be charged as a supplemental|

{: .note}
The mileage displays the exact mileage between the user input coordinates and the system locations coordinates. 50 miles is considered the maximum standard distance between a user input coordinates and the system locations coordinates. While extraMileage displays the additional miles beyond 50 miles from the user input coordinates to the farthest outermost coordinate considered part of the resulting location.

### Examples
#### Search International Locations
{: .no_toc }
 
Request:
```json
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: [ api-key ]
{
    "latitude": -37.8409,
    "longitude": 144.9464,
    "countryCode": "AU"
}
```
Response:
```json
HTTP/1.1 201 OK
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
                    "latitude": -37.8136,
                    "longitude": 144.963,
                    "radius": 47,
                    "id": 1
                },
                "name": "Melbourne",
                "state": "Vic",
                "id": 1,
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
#### Search CONUS Locations
{: .no_toc }
  
Request:
```json
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: [ api-key ]

{
    "latitude": 42.3611,
    "longitude": -71.057,
    "countryCode": "US",
    "zip3": "021"
}
```
Response:
```json
HTTP/1.1 200 OK
{
    "latitude": 42.3611,
    "longitude": -71.057,
    "countryCode": "US",
    "zip3": "021",
    "locations": {
        "data": [
            {
                "id": 1,
                "type": "zip3",
                "countryCode": "US",
                "geography": null,
                "name": "021 - Boston, MA",
                "state": "MA",
                "country": "USA",
                "display": "021 - Boston, MA",
                "city": "Boston",
                "zip3": "021",
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
