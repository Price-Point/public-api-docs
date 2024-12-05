---
layout: default
title: Portal API Example
nav_order: 5
---
# PricePoint Portal API Example
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }
1. TOC
{:toc}

## Get Your Corporate Account ID
To determine which account your organization has been assigned simple list them, all accounts returned will be valid for your use.
Request:
```json
GET /api/v1/corporateAccounts
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
In the example above, one corporate account with an ID of 12345 is accessable. 12345 is the [corpId] for subsequent API requests.
## Get the Currency
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
## Find the Origins and Destinations
Moves can use many types of locations such as Markets and Zip5s. Use the [Location Search](./references/locations.html#location-search) route to determine which locations to use as the origins / destinations for a move. To do this you will need to geocode your transferee's address and supply the long,lat,country, and zip5 if the move is in the US. 

{: .note}
Longitude and latitude are required for international moves. If you want to create a conus move, you only need to supply the countryCode as "US" and the zip5 as a string.
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
HTTP/1.1 201 Created
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
Request:
```json
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: [ api-key ]
{
    "latitude": 39.7392,
    "longitude": -104.9903,
    "countryCode": "US",
    "zip5": "80003"
}
```
Response:
```json
HTTP/1.1 201 Created
{
    "latitude": 39.7392,
    "longitude": -104.9903,
    "countryCode": "US",
    "zip5": "80003",
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
{: .note}
For CONUS moves, all of the locations from the search should be provided to the move as origins/destinations. For international moves, only the closest market location should be given to the move.
## Get the Client
To select which of your clients the move is for, simple list the clients and pick one.
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/clients
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
## Creating a Move
You are now have all of the information necessary to create a move. In this example we will construct a CONUS move with a single Tier 1 shipment. The first step is to construct a shipment that has the size and mode information for the shipment
```json
{
  "name": "api test shipment",
  "updates": {
    "data": [
      {
        "modes": {
          "data": [
            "Tier 1"
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
Now we can put all of the information from previous steps together to create a move. It may take some time to complete so it is considered a [Long Running Request](./api_conventions/async.html).
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves
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
                  "Tier 1"
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
HTTP/1.1 202 Created
location: /api/v1/requestStatus/[requestId]/status
retry-after: 1
{}
```
The [moveId] for the created move will be returned when the [Long Running Request](./api_conventions/async.html) is complete.  
## Getting Prices
All prices are on a per shipment bases, so first find the shipment id (aka [shipmentId])
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments
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

Before you can get prices, you need to make a post prices request which is considered a [Long Running Request](./api_conventions/async.html).
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/prices
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
Once the post prices request is completed, you can request all the current prices for the shipment. Note that certain conditions will require you to post prices again to get the lastet prices. The prices route will return a 400 and tell you when you need to post prices again.
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/prices
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 Ok
{
  "data": [
    {
      "price": 100,
      "id": "f747ac10-726b-11ed-a8dd-b56801d2469e",
      "tariffs": {
        "data": [
          {
            "id": 1
          }
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
      },
      "booker": {
        "id": 1,
        "name": "Supplier 1"
      }
    },
    {
      "price": 105,
      "id": "f748e490-726b-11ed-a8dd-b56801d2469e",
      "tariffs": {
        "data": [
          {
            "id": 2
          }
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
      },
      "booker": {
        "id": 2,
        "name": "Supplier 2"
      }
    },
    {
      "price": 98,
      "id": "f74959c1-726b-11ed-a8dd-b56801d2469e",
      "tariffs": {
        "data": [
          {
            "id": 3
          }
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
      },
      "booker": {
        "id": 3,
        "name": "Supplier 4"
      }
    },
    {
      "price": 94,
      "id": "f74980d3-726b-11ed-a8dd-b56801d2469e",
      "tariffs": {
        "data": [
          {
            "id": 4
          }
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
      },
      "booker": {
        "id": 4,
        "name": "Supplier 4"
      }
    }
  ]
}
```
## Updating a Shipment
As you learn more from the transferee you may want to update the shipment to get more accurate pricing. Updating a shipment is considered a [Long Running Request](./api_conventions/async.html).
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/updates
Content-Type: application/json
x-api-key: [ api-key ]
{
  "modes": {
    "data": [
      "Tier 1"
    ]
  },
  "units": {
    "data": [
      {
        "unit": "lb",
        "amount": "2100",
        "type": "weight"
      },
      {
        "unit": "cuft",
        "amount": "350",
        "type": "volume"
      }
    ]
  }
}
```
Response:
```json
HTTP/1.1 202 Created
location: /api/v1/requestStatus/[requestId]/status
retry-after: 1
{}
```
After the update has finished processing, you can query the prices route again to get the latest prices. You can view all the shipment updates as shown below.
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/updates
Content-Type: application/json
x-api-key: [ api-key ]
{
  "modes": {
    "data": [
      "Tier 3"
    ]
  },
  "units": {
    "data": [
      {
        "unit": "lb",
        "amount": "2100",
        "type": "weight"
      },
      {
        "unit": "cuft",
        "amount": "350",
        "type": "volume"
      }
    ]
  }
}
```
Response:
```json
HTTP/1.1 201 Created
{
    "id": 3
}
```
## Awarding the Shipment
Once you have selected a supplier for your shipment, from those that have a valid price, you can award that shipment to the supplier by submitting the stage of the shipment. Submitting a stage is considered a [Long Running Request](./api_conventions/async.html).
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/submitStage
Content-Type: application/json
x-api-key: [ api-key ]
{
 
  "price": 5033.29398328895,
  "id": "e700c5f0-726e-11ed-b98a-e75c56480dae",
  "tariffs": {
    "data": [
      {
        "id": 16150253
      }
    ]
  },
  "units": {
    "data": [
      {
        "unit": "lb",
        "amount": "2100",
        "type": "weight"
      },
      {
        "unit": "cuft",
        "amount": "350",
        "type": "volume"
      }
    ]
  },
  "booker": {
    "id": 1,
    "name": "Supplier 1"
  }
}
```
Response:
```json
HTTP/1.1 202 Created
location: /api/v1/requestStatus/[requestId]/status
retry-after: 1
{}
```