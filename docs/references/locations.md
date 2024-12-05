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
The Locations API supports discovery of Location objects that represent physical locations used to supply service. Locations are often to be used as an origins and destinations for [Moves](corporate_accounts.html#move-resource). There are 3 supported types of locations.

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
Market locations in PricePoint are used to determine the area in which a mover may provide a service, they are based on a service radius of 50 miles. Any residence location within 50 miles of city-center of the location, is considered included in the rates. If a residence falls outside that radius any service will likely incur a supplemental charge for additional mileage.
### Zip5
For service delivered in the United States (CONUS) Zip5s are also used to specify location of those services. We use Zip5 as designated by the USPS. Using Zip5 allows complete location coverage of all lower 48 states.

A Zip5 Includes all the properties of [Location Resource](#location-resource) and adds the following fields.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
|`zip5`|string|the 5 digits of the US zip code|
|`city`|string|the name of the associated city|


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
|`zip5`|string|(options) a zip5 to consider for CONUS moves|
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
    "countryCode": "US",
    "zip5": "19146"
}
```
Response:
```json
HTTP/1.1 200 OK
{
  "countryCode": "US",
  "zip5": "19146",
  "locations": {
    "data": [
      {
        "type": "conusMetro",
        "countryCode": "US",
        "geography": null,
        "date": "2024-05-14T14:33:50.000Z",
        "name": "Philadelphia",
        "countryId": 3,
        "state": "PA",
        "regionId": null,
        "geopoliticalRoadAreaId": null,
        "id": 7232,
        "country": "USA",
        "display": "USA - Philadelphia, PA",
        "zip3s": {
          "data": [
            "080",
            "081",
            "082",
            "083",
            "085",
            "086",
            "189",
            "190",
            "191",
            "192",
            "193",
            "194",
            "195",
            "197",
            "198",
            "219"
          ],
          "limit": 16,
          "offset": 0,
          "length": 16
        },
        "mileage": 0,
        "extraMileage": 0
      },
      {
        "type": "zip3",
        "zip3": "191",
        "geography": null,
        "date": "2022-08-31T17:32:37.000Z",
        "name": "191 - Philadelphia, PA",
        "countryId": 3,
        "state": "PA",
        "regionId": null,
        "geopoliticalRoadAreaId": null,
        "id": 4949,
        "country": "USA",
        "countryCode": "US",
        "display": "191 - Philadelphia, PA",
        "serviceArea": 673,
        "city": "Philadelphia",
        "locationId": 4949,
        "mileage": 0,
        "extraMileage": 0
      },
      {
        "type": "zip5",
        "zip5": "19146",
        "geography": null,
        "date": "2024-06-19T15:03:42.000Z",
        "name": "19146 - Philadelphia, PA",
        "countryId": 3,
        "state": "PA",
        "regionId": null,
        "geopoliticalRoadAreaId": null,
        "id": 24646,
        "country": "USA",
        "countryCode": "US",
        "display": "19146 - Philadelphia, PA",
        "city": "Philadelphia",
        "locationId": 24646,
        "mileage": 0,
        "extraMileage": 0
      }
    ],
    "limit": 3,
    "offset": 0,
    "length": 3
  }
}
```
