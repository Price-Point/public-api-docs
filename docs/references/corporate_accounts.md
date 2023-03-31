---
layout: default
title: Corporate Accounts
nav_order: 3
parent: References
---
# Corporate Accounts
{: .no_toc }

{: .note }
This reference includes all supported resources and completely documents all supported fields. Fields not mentioned in this document are subject to change without notice. All referenced resources will remain backwards compatible in their supported fields. 

## Table of Contents
{: .no_toc .text-delta }
1. TOC
{:toc}
## Corporate Accounts Resource
A corporate account is a [Top Level Resource](../api_conventions/url_structure.html#top-level-resource). A corporate account is the purchaser of Door to Door moves from their suppliers. 

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name of the corporate account|
| `clients`|[Client](#client-resource)[]|the clients associated with the account|
| `allowances`|[Allowance](#allowance-resource)[]|all available allowances|
| `moves`|[Move](#move-resource)[]|all the accounts moves|
| `rateRequests`|[RateRequest](#rate-requests-resource)[]|all rate request for the account|

### Examples
#### Read Corporate Account
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "id": 1,
    "name": "my corporate account"
}
```

## Client Resource
A client is a representation of a customer of one of a corporate account. They may be used to track data by the corporate account's end client. E.g. to track and specify the different department or internal unit that is responsible for the move. Clients and their respective referral fees are set up by PricePoint Admin during the implementation process.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name of the client|
| `type`|string|the type of move (international or CONUS) this client is associated with|

### Examples
#### List Clients
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/clients
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "limit": 50,
    "offset": 0,
    "length": 50,
    "data": [
        {
            "id": 1,
            "name": "client 1",
            "type": "international"
        },
        {
            "id": 2,
            "name": "client 2",
            "type": "conus"
        },
    ...
    ]
}
```

## Allowance Resource
Allowances are used to provide default for the mode and units used to create shipments.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name of the allowance|
| `mode`|[Mode](#modes)|the mode of the allowance|
| `units`|[Unit](#unit-resource)

### Examples
#### List Allowances by Mode With Units  
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/allowances?mode=fclCased,fclLoose&expand=units
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
  "limit": 50,
  "offset": 0,
  "length": 50,
  "data": [
    {
      "units": {
        "limit": 50,
        "offset": 0,
        "length": 3,
        "data": [
          {
            "name": "weight",
            "unit": "lb",
            "value": "14300"
          },
          {
            "name": "volume",
            "unit": "cuft",
            "value": "2200"
          },
          {
            "name": "container",
            "unit": "container",
            "value": "40"
          }
        ]
      },
      "id": 1,
      "name": "Ocean - 40' container",
      "mode": "fclLoose"
    },
    {
      "units": {
        "limit": 50,
        "offset": 0,
        "length": 2,
        "data": [
          {
            "name": "weight",
            "unit": "lb",
            "value": "1404"
          },
          {
            "name": "volume",
            "unit": "cuft",
            "value": "216"
          }
        ]
      },
      "id": 2,
      "name": "LCL - 180 cuft net",
      "mode": "fclCased"
    },
    ...
  ]
}
```

## Move Resource
A move contains all the information needed for pricing of a physical move. Starting locations can include multiple markets / zip3s to ensure you receive the best pricing.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name of the move, must be unique|
| `type`|string|the type of move. valid fields: ['international', 'conus']|
| `origins`|[Location](locations.html#location-resource)|The starting point(s) for the move|
| `destinations`|[Location](locations.html#location-resource)|The ending point(s) for the move|
| `client`|[Client](#client-resource)|the client for the move|
| `currency`|[Currency](currencies.html#currency-resource)|a currency object containing the id of the currency|
| `shipments`|[Shipment](#shipment-resource)[]|the shipments that will be part of this move|
| `metadata` |[Metadata](#metadata-resource)[]|custom user data|

{: .note }
A move requires at least 1 shipment

### Examples
#### List Moves
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "limit": 50,
    "offset": 0,
    "length": 50,
    "data": [
        {
            "id": 1,
            "name": "move 1",
            "type": "international"
        },
        {
            "id": 2,
            "name": "move2 2",
            "type": "conus"
        },
    ...
    ]
}
```

#### Read Move With Expands
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]?expand=shipments&shipments.expand=updates
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
    "shipments": {
        "limit": 50,
        "offset": 0,
        "length": 1,
        "data": [
            {
                "updates": {
                    "limit": 50,
                    "offset": 0,
                    "length": 1,
                    "data": [
                        {
                            "id": 1,
                            "status": "bid",
                            "inventoryCount": -1
                        }
                    ]
                },
                "id": 1,
                "name": "Shipment (1)"
            }
        ]
    },
    "id": 1,
    "name": "api-demo",
    "type": "international"
}
```

#### Create Move
{: .no_toc }
Creating a move is a [Long Running Request](../api_conventions/async.html).
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves
Content-Type: application/json
x-api-key: [ api-key ]
{
  "name": "api-test",
  "type": "international",
  "origins": {
    "data": [{
      "type": "market",
      "id": 1,
      "name": "New York City",
      "country": "USA",
      "display": "USA - New York City"
    }],
  },
  "destinations": {
    "data": [{
      "type": "market",
      "id": 2,
      "name": "London",
      "country": "United Kingdom",
      "display": "United Kingdom - London"
    }]
  },
  "currency": {
    "id": 1,
    "name": "US Dollars",
    "symbol": "USD",
    "priceInUsd": 1
  },
  "client": {
      "id": 1,
      "name": "client 1",
      "type": "international"
  },
  "shipments": {
    "data": [
      {
        "name": "Shipment (1)",
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
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1
```

## Rate Requests Resource
The Rate Request resource is used to solicit pricing from your suppliers for a specific shipment.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `moveId`|number|the id of the move the rate request was submitted for|
| `moveName`|string|the name of the move the rate request was submitted for|
| `shipmentId`|string|the id of the shipment the rate request was submitted for|
| `shipmentName`|string|the name of the shipment the rate request was submitted for|
| `mode`|[Mode](#modes)|The mode of the shipment|
| `bookerId`|number|The id of the supplier the rate was requested from|
| `date`|string|the date the request was submitted|

### Examples
#### Rate Requests for Move
{: .no_toc }
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/rateRequests?moveName=[move.name]
Content-Type: application/json
x-api-key: [ api-key ]
{}
```
Response:
```json
HTTP/1.1 201 OK
{
  "limit": 50,
  "offset": 0,
  "length": 2,
  "data": [
    {
      "id": 1,
      "moveId": 1,
      "moveName": "Move (1)",
      "shipmentId": 1,
      "shipmentName": "Shipment (1)",
      "mode": "fclLoose",
      "bookerId": 1,
      "date": "2022-01-01T00:00:00.000Z"
    },
    {
      "id": 2,
      "moveId": 1,
      "moveName": "Move (1)",
      "shipmentId": 1,
      "shipmentName": "Shipment (1)",
      "mode": "fclLoose",
      "bookerId": 2,
      "date": "2022-01-01T00:00:00.000Z"
    }
  ]
}
```

## Modes
Modes are used to define the type of service that is needed for a shipment. Most often these are used to determine the means of transportation.

|Mode|Move Type|Description|
| --------------- | -------- |
| `fclLoose` | International |MIRANDA TODO a loose packed full container|
| `fclCased` | International |MIRANDA TODO a case packed full container|
| `lcl` | International |MIRANDA TODO a less than container load|
| `air` | International |MIRANDA TODO air cargo shipment|
| `roadExport` | International |MIRANDA TODO road shipment with export packing|
| `roadBlanket` | International |MIRANDA TODO road shipment with blanket packing|
| `Tier 1` | CONUS |MIRANDA TODO|
| `Tier 2` | CONUS |MIRANDA TODO|
| `Tier 3` | CONUS |MIRANDA TODO|
| `Tier 4` | CONUS |MIRANDA TODO|
| `Tier 5` | CONUS |MIRANDA TODO|
| `Tier 6` | CONUS |MIRANDA TODO|

## Shipment Resource
MIRANDA TODO A shipment represents a part of a physical move. EX: there could be a move with 2 shipments. One air shipment and one fclLoose shipment. 

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name of the shipment|
| `updates`|[Update](#update-resource)[]|the history of the shipment, the latest update is used for the /prices route|
| `services`|[Service](#service-resource)[]|all of the services order from a supplier for the shipment|
| `chargeDetails`|[ChargeDetails](#charge-details-resource)[]| contains pricing information from past stages|
| `metadata`|[Metadata](#metadata-resource)[]|an array of metadata|

### Examples
#### List Move Shipments
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 OK
{
  "limit": 50,
  "offset": 0,
  "length": 2,
  "data": [{
            "id": 44427,
            "name": "Shipment (1)",
        },
        {
            "id": 44444,
            "name": "Shipment (2)",
        }
    ]
}
```

#### Create New Shipment Request
{: .no_toc }

{: .note }
Shipments require at least 1 update. Creating a shipment is a [Long Running Request](../api_conventions/async.html).

```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments
Content-Type: application/json
x-api-key: [ api-key ]
{
  "name": "Shipment (2)",
  "updates": {
    "data": [
      {
        "modes": {
          "data": [
            "lcl"
          ]
        },
        "units": {
          "data": [
            {
              "unit": "lb",
              "amount": "148",
              "type": "weight"
            },
            {
              "unit": "cuft",
              "amount": "22",
              "type": "volume"
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
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1
```
### Price Shipment
The price for a shipment is based on the most recent update and will be returned as a [Charge Details](#charge-details-resource). The first time you try to get prices you will recieve a 404 telling you that you need to post prices first. This will also happen if a certain amount of time has elapsed or if something substantial about the shipment changes.
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/prices
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 400 Accepted
{
    "status": 404,
    "title": "Not found",
    "details": "No valid prices, you need to post prices",
    "instance": "https://pricepoint.pricepointmoves.com/api/v1/error"
}
```
To resolve that, simply make a post prices request and wait for it to complete.
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/prices
Content-Type: application/json
x-api-key: [ api-key ]
{}
```
Response:
```json
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1
```

Now you can request prices again and recieve all the shipment prices.

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
      "price": 100.1,
      "tariffs": {
        "data": [
          {
            "id": 1
          },
          {
            "id": 2
          },
          {
            "id": 4
          }
        ]
      },
      "units": {
        "data": [
          {
            "amount": "6500",
            "unit": "lb"
          },
          {
            "amount": "1000",
            "unit": "cuft"
          },
          {
            "amount": "20",
            "unit": "container"
          }
        ]
      },
      "booker": {
        "id": 1,
        "name": "Supplier 1"
      }
    },
    {
      "price": null,
      "tariffs": {
        "data": [
          {
            "id": 1
          },
          {
            "id": 2
          },
          {
            "id": 4
          }
        ]
      },
      "units": {
        "data": [
          {
            "amount": "6500",
            "unit": "lb"
          },
          {
            "amount": "1000",
            "unit": "cuft"
          },
          {
            "amount": "20",
            "unit": "container"
          }
        ]
      },
      "booker": {
        "id": 2,
        "name": "Supplier 2"
      }
    }
  ]
}
```

### Award Shipment
MIRANDA TODO Awarding a shipment lets your supplier know that you are ready for them to start working on the shipment. To award a shipment you will have to have a [Charge Details](#charge-details-resource) from the prices route with a price that is not null. Once a shipment is awarded a corresponding [Service](#service-resource) will be created and it is no longer necessary to create a prices request to get pricing. Awarding a shipment is a [Long Running Request](../api_conventions/async.html).
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/submitStage
Content-Type: application/json
x-api-key: [ api-key ]
{
  "price": 100.1,
  "tariffs": {
    "data": [
      {
        "id": 1
      },
      {
        "id": 2
      },
      {
        "id": 4
      }
    ]
  },
  "units": {
    "data": [
      {
        "amount": "6500",
        "unit": "lb"
      },
      {
        "amount": "1000",
        "unit": "cuft"
      },
      {
        "amount": "20",
        "unit": "container"
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
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1
```

### Cancel Shipment
MIRANDA TODO If a shipment becomes extraneous it can be canceled and will no longer be executed by the supplier. Canceling a shipment is a [Long Running Request](../api_conventions/async.html).
If a shipment is in fact not needed, you can cancel it with the route below. The response is a Location to check the status.\
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/cancel
Content-Type: application/json
x-api-key: [ api-key ]
{}
```
Response:
```json
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1
```

## Update Resource
MIRANDA TODO An update is a record of the updates made to a shipments, it functions as an audit trail for changes to modes, weight, volume, etc. 

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `status`|[Status](#status)|(readonly) what stage this update is for|
| `inventoryCount`|number|MIRANDA TODO the number of items in the shipment, usually filled out after the pack stage|
| `modes`|[Mode](#modes)[]|the modes of the shipment. most common to only have one.|
| `units`|[Unit](#unit-resource)[]|contains the units. I.E. weight, volume, container size information|

### Examples
#### List Most Recent Update With Units and Modes
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/updates?sort=id:desc&limit=1&expand=units
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 Ok
{
  "limit": 1,
  "offset": 0,
  "length": 1,
  "data": [
    {
      "modes": {
        "limit": 50,
        "offset": 0,
        "length": 1,
        "data": [
          "fclLoose"
        ]
      },
      "units": {
        "limit": 50,
        "offset": 0,
        "length": 3,
        "data": [
          {
              "id": 1,
              "unit": "lb",
              "amount": "6500",
              "type": "weight"
          },
          {
              "id": 2,
              "unit": "cuft",
              "amount": "1000",
              "type": "volume"
          },
          {
              "id": 3,
              "unit": "container",
              "amount": "20",
              "type": "container"
          }
        ]
      },
      "id": 1,
      "status": "pack",
      "inventoryCount": 10
    }
  ]
}
```
### Examples
#### Create an update
{: .no_toc }
Creating an update is a [Long Running Request](../api_conventions/async.html).
Request:
```json
POST /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/updates
Content-Type: application/json
x-api-key: [ api-key ]
{
  "modes": {
    "data": [
      "fclLoose"
    ]
  },
  "units": {
    "data": [
      {
          "unit": "lb",
          "amount": "6500",
          "type": "weight"
      },
      {
          "unit": "cuft",
          "amount": "1000",
          "type": "volume"
      },
      {
          "unit": "container",
          "amount": "20",
          "type": "container"
      }
    ]
  },
  "inventoryCount": 10
}
```
Response:
```json
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1
```

## Service Resource
MIRANDA TODO A service represent an order that you have placed with a supplier, the are created automatically when you award a shipment.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `bookerId`|number|the identifier of the supplier of the service|
| `quoteId`|number|the identifier of the quote used to create the service|

### Example
#### List Services
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/services
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
      "id": 19189,
      "bookerId": 31,
      "quoteId": 1044247
    }
  ]
}
```

## Charge Details Resource
MIRANDA TODO Charge Details provide a record of the pricing of a shipment as it moves through the workflow, they are created automatically when a shipment is awarded or a supplier completes a [stage](#status). A Charge Details are returned when [Pricing A Shipment](#price-shipment). A Charge Details with a not null price is required to [Award A Shipment](#award-shipment).

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `price`|number or null|the overall price |
| `tariffs`|[Tariff](#tariff-resource)[]|the tariffs used to generate the price|
| `units`|[Unit](#unit-resource)[]|the units used to generate the price|
| `booker`|[Booker](#booker-resource)[]|the supplier for the charge details|

### Example
#### List Charge Details With Tariffs and Units
{: .no_toc }
Request:
```json
GET /api/v1/corporateAccounts/[corpId]/moves/[moveId]/shipments/[shipmentId]/chargeDetails?expand=tariffs,units
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 200 Ok
{
  "data": [
    {
      "id": 1,
      "price": 100.1,
      "tariffs": {
        "data": [
          {
            "id": 1
          },
          {
            "id": 2
          },
          {
            "id": 4
          }
        ]
      },
      "units": {
        "data": [
          {
            "amount": "6500",
            "unit": "lb"
          },
          {
            "amount": "1000",
            "unit": "cuft"
          },
          {
            "amount": "20",
            "unit": "container"
          }
        ]
      },
      "booker": {
        "id": 1,
        "name": "Supplier 1"
      }
    },
    {
      "id": 2,
      "price": null,
      "tariffs": {
        "data": [
          {
            "id": 1
          },
          {
            "id": 2
          },
          {
            "id": 4
          }
        ]
      },
      "units": {
        "data": [
          {
            "amount": "6500",
            "unit": "lb"
          },
          {
            "amount": "1000",
            "unit": "cuft"
          },
          {
            "amount": "20",
            "unit": "container"
          }
        ]
      },
      "booker": {
        "id": 2,
        "name": "Supplier 2"
      }
    }
  ]
}
```

## Metadata Resource
MIRANDA TODO Custom information that the users is allowed to store about a [Move](#move-resource) or a [Shipment](#shipment-resource).

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name: EX: 'allowanceId'|
| `value`|string|the value: EX: '344'|

## Unit Resource
Units are used to calculate [Prices](#price-shipment) and stored as part of an [Update](#update-resource). They represent the physical size of the shipment.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `type`|string|the type of unit. one of ['weight','volume','container']|
| `amount`|string|always a number except for the special case of a container with size '40hc'|
| `unit`|string|one of ['lb','kg','cuft','cbm','container']|

## Status
MIRANDA TODO describe statuses

|Status|Description|
| --------------- | -------- |
| `bid` |MIRANDA TODO|
| `survey`|MIRANDA TODO|
| `pack` |MIRANDA TODO|
| `delivery`|MIRANDA TODO|
| `additional`|MIRANDA TODO|
| `complete` |MIRANDA TODO|

## Tariff Resource
MIRANDA TODO describe tariffs

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|

## Booker Resource
MIRANDA TODO describe bookers

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name of the supplier|