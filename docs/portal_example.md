---
layout: default
title: Portal API Example
nav_order: 3
---
# PricePoint Portal API Example
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }
1. TOC
{:toc}

## Get Your Account
To determine which account your organization has been assigned simple list them, all accounts returned will be valid for your use.
Request:
```json
GET /api/v4/corporateAccounts
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "data": [
        {
            "id": 12345,
            "name": "Awesome Corporate Account"
        }
    ]
}
```
## Get The Currency
All move prices are converted from the supplier's currency to the accounts desired currency. To find a currency with a specific symbol, simple list the currencies and filter for that symbol.
Request:
```json
GET /api/v2/currencies?symbol=USD
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "data": [
        {
            "symbol": "USD",
            "name": "US Dollars",
            "priceInUsd": 1,
            "id": 1
        }
    ],
    "limit": 50,
    "offset": 0,
    "length": 1
}
```
## Find The Origin and Destination
Moves are based on Market and Zip Locations, the easiest way to determine which locations to use as the origins / destinations for a move is to use the location search. To do this you will need to geocode your transferee's address and supply the long,lat,country, and zip3 if the move is in the US.
Request:
```json
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: [ api-key ]
{
    "latitude": 35.1983,
    "longitude": -111.6513,
    "countryCode": "US",
    "zip3": "860"
}
```
Response:
```json
HTTP/1.1 201 Created
{
    "latitude": 35.1983,
    "longitude": -111.6513,
    "countryCode": "US",
    "zip3": "860",
    "locations": {
        "data": [
            {
                "type": "zip3",
                "countryCode": "US",
                "geography": null,
                "name": "860 - Flagstaff, AZ",
                "state": "AZ",
                "id": 4121,
                "country": "USA",
                "display": "860 - Flagstaff, AZ",
                "city": "Flagstaff",
                "zip3": "860",
                "mileage": 0,
                "extraMileage": 0
            },
            {
                "type": "market",
                "countryCode": "US",
                "geography": {
                    "latitude": 35.1983,
                    "longitude": -111.651,
                    "radius": 47,
                    "type": "circle"
                },
                "name": "Flagstaff",
                "state": "AZ",
                "id": 2617,
                "country": "USA",
                "display": "USA - Flagstaff, AZ",
                "mileage": 0.0167770224,
                "extraMileage": 0
            }
        ],
        "limit": 2,
        "offset": 0,
        "length": 2
    }
}
```
Request:
```json
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: [ api-key ]
{
    "latitude": 39.7392,
    "longitude": -104.9903,
    "countryCode": "US",
    "zip3": "800"
}
```
Response:
```json
HTTP/1.1 201 Created
{
    "latitude": 39.7392,
    "longitude": -104.9903,
    "countryCode": "US",
    "zip3": "800",
    "locations": {
        "data": [
            {
                "type": "zip3",
                "countryCode": "US",
                "geography": null,
                "name": "800 - Denver, CO",
                "state": "CO",
                "id": 4201,
                "country": "USA",
                "display": "800 - Denver, CO",
                "city": "Denver",
                "zip3": "800",
                "mileage": 0,
                "extraMileage": 0
            },
            {
                "type": "market",
                "countryCode": "US",
                "geography": {
                    "latitude": 39.7392,
                    "longitude": -104.99,
                    "radius": 47,
                    "type": "circle"
                },
                "name": "Denver",
                "state": "CO",
                "id": 1921,
                "country": "USA",
                "display": "USA - Denver, CO",
                "mileage": 0.0161556512,
                "extraMileage": 0
            },
            {
                "type": "market",
                "countryCode": "US",
                "geography": {
                    "latitude": 40.1672,
                    "longitude": -105.102,
                    "radius": 47,
                    "type": "circle"
                },
                "name": "Longmont",
                "state": "CO",
                "id": 2578,
                "country": "USA",
                "display": "USA - Longmont, CO",
                "mileage": 30.191805236799997,
                "extraMileage": 0
            },
            {
                "type": "market",
                "countryCode": "US",
                "geography": {
                    "latitude": 38.8339,
                    "longitude": -104.821,
                    "radius": 47,
                    "type": "circle"
                },
                "date": "2022-09-09T16:15:05.000Z",
                "name": "Colorado Springs",
                "state": "CO",
                "id": 2611,
                "country": "USA",
                "display": "USA - Colorado Springs, CO",
                "mileage": 63.2729865536,
                "extraMileage": 16.2729865536
            }
        ],
        "limit": 4,
        "offset": 0,
        "length": 4
    }
}
```
## Get The Client
To select which of your clients the move is for, simple list the clients and pick one.
Request:
```json
GET /api/v4/corporateAccounts/[corpId]/clients
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 Ok
{
    "limit": 50,
    "offset": 0,
    "length": 1,
    "data": [
        {
            "id": 1,
            "name": "My Client",
            "type": "conus"
        }
    ]
}
```
## Creating A Move
You are now have all of the information necessary to create a move. In this example we will construct a CONUS move with a single Tier 1 shipment. The first step is to construct a shipment that has the size and mode information for the shipment
```json
{
  "name": "api test shipment",
  "updates": {
    "data": [
      {
        "modes": {
          "data": [
            "air"
          ]
        },
        "units": {
          "data": [
            {
              "unit": "lb",
              "amount": "2325",
              "type": "weight"
            },
            {
              "unit": "cuft",
              "amount": "400",
              "type": "volume"
            }
          ]
        }
      }
    ]
  }
}
```
Now we can put all of the information from previous steps together to create a move.
Request:
```json
POST /api/v4/corporateAccounts/[corpId]/moves
Content-Type: application/json
x-api-key: [ api-key ]
{
  "name": "api-test",
  "type": "conus",
  "currency": {
    "symbol": "USD",
    "name": "US Dollars",
    "priceInUsd": 1,
    "id": 1
  },
  "origins": {
    "data": [
      {
        "type": "zip3",
        "countryCode": "US",
        "geography": null,
        "name": "860 - Flagstaff, AZ",
        "state": "AZ",
        "id": 4121,
        "country": "USA",
        "display": "860 - Flagstaff, AZ",
        "city": "Flagstaff",
        "zip3": "860",
        "mileage": 0,
        "extraMileage": 0
      },
      {
        "type": "market",
        "countryCode": "US",
        "geography": {
          "latitude": 35.1983,
          "longitude": -111.651,
          "radius": 47,
          "type": "circle"
        },
        "name": "Flagstaff",
        "state": "AZ",
        "id": 2617,
        "country": "USA",
        "display": "USA - Flagstaff, AZ",
        "mileage": 0.0167770224,
        "extraMileage": 0
      }
    ],
    "limit": 2,
    "offset": 0,
    "length": 2
  },
  "destinations": {
    "data": [
      {
        "type": "zip3",
        "countryCode": "US",
        "geography": null,
        "name": "800 - Denver, CO",
        "state": "CO",
        "id": 4201,
        "country": "USA",
        "display": "800 - Denver, CO",
        "city": "Denver",
        "zip3": "800",
        "mileage": 0,
        "extraMileage": 0
      },
      {
        "type": "market",
        "countryCode": "US",
        "geography": {
          "latitude": 39.7392,
          "longitude": -104.99,
          "radius": 47,
          "type": "circle"
        },
        "name": "Denver",
        "state": "CO",
        "id": 1921,
        "country": "USA",
        "display": "USA - Denver, CO",
        "mileage": 0.0161556512,
        "extraMileage": 0
      },
      {
        "type": "market",
        "countryCode": "US",
        "geography": {
          "latitude": 40.1672,
          "longitude": -105.102,
          "radius": 47,
          "type": "circle"
        },
        "name": "Longmont",
        "state": "CO",
        "id": 2578,
        "country": "USA",
        "display": "USA - Longmont, CO",
        "mileage": 30.191805236799997,
        "extraMileage": 0
      },
      {
        "type": "market",
        "countryCode": "US",
        "geography": {
          "latitude": 38.8339,
          "longitude": -104.821,
          "radius": 47,
          "type": "circle"
        },
        "date": "2022-09-09T16:15:05.000Z",
        "name": "Colorado Springs",
        "state": "CO",
        "id": 2611,
        "country": "USA",
        "display": "USA - Colorado Springs, CO",
        "mileage": 63.2729865536,
        "extraMileage": 16.2729865536
      }
    ],
    "limit": 4,
    "offset": 0,
    "length": 4
  },
  "client": {
    "id": 1,
    "name": "My Client",
    "type": "conus"
  },
  "shipments": {
    "data": [
      {
        "name": "api test shipment",
        "updates": {
          "data": [
            {
              "modes": {
                "data": [
                  "air"
                ]
              },
              "units": {
                "data": [
                  {
                    "unit": "lb",
                    "amount": "2325",
                    "type": "weight"
                  },
                  {
                    "unit": "cuft",
                    "amount": "400",
                    "type": "volume"
                  }
                ]
              }
            }
          ]
        }
      }
    ]
  }
}
```
Response:
```json
HTTP/1.1 201 Created
{
    "id": 1
}
```
## Getting Prices
All prices are on a per shipment bases, so first find the shipment id
Request:
```json
GET /api/v4/corporateAccounts/[corpId]/moves/[moveId]/shipments
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 Ok
{
    "limit": 50,
    "offset": 0,
    "length": 1,
    "data": [
        {
            "id": 1,
            "name": "api test shipment",
        }
    ]
}
```
We can now create a quote for the shipment, this will find all of the available tariffs from your suppliers. It may take some time to complete so it is considered a long running request.
Request:
```json
POST /api/v4/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/quotes
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 202 Created
location: /api/v1/requestStatus/[requestId]/status
retry-after: 1
{}
```
Once the quote is successfully created you can view all your quotes, the one with the largest id will be used for any price queries
Request:
```json
GET /api/v4/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/quotes
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 Ok
{
    "limit": 50,
    "offset": 0,
    "length": 1,
    "data": [
        {
            "id": 144265,
            "userId": 3707,
            "date": "2022-12-02T17:37:46.000Z",
            "status": "bid"
        }
    ]
}
```
You can also request prices based on the latest quote.
Request:
```json
GET /api/v4/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/prices
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 Ok
{
    TODO FINISH THIS
}
```