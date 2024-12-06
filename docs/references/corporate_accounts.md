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
| `fclLoose` | International |Shipment loose loaded into a standard 20’, 40’ of 40’High Cube container|
| `fclCased` | International |Shipment packed into standard wood lift vans, and lift vans loaded into a standard 20’ or 40’ container|
| `lcl` | International |Less than Container Load shipment packed into standard wood lift vans, and lift vans co-loaded into a standard 20’ or 40’ container by a consolidating NVOCC|
| `air` | International |Shipment packed into air boxes and shipped via air cargo|
| `roadExport` | International |Shipment packed as an international shipment (with one-time use packing materials) and loaded into a truck. This can be a FTL / exclusive use of truck or LTL / co-loaded.|
| `roadBlanket` | International |Shipment packed as a domestic shipment utilizing reusable packing materials such as moving blankets and loaded into a truck. This can be a FTL / exclusive use of truck or LTL / co-loaded.|
| `Tier 1` | CONUS |Full-service, van line. Shipment is packed and loose loaded into truck and includes, packing materials and labor, loading labor, unloading labor, unpacking labor, and day of debris removal. |
| `Tier 2` | CONUS |Partial-service, van line. Shipment is packed and loose loaded into truck and includes, packing materials and labor, loading labor, and unloading labor. Excludes unpacking labor and debris removal.|
| `Tier 3` | CONUS |Full-service, containerized. Shipment is packed and loaded into reusable containers, and containers are shipped via truck. Service includes, packing materials and labor, loading labor, unloading labor, unpacking labor, and day of debris removal. |
| `Tier 4` | CONUS |Partial-service, containerized. Shipment is packed and loaded into reusable containers, and containers are shipped via truck. Service includes, packing materials and labor, loading labor, and unloading labor. Excludes unpacking labor, and debris removal.|
| `Tier 5` | CONUS |Labor only, containerized. Shipment is packed by the transferee, mover loads packed boxes into reusable containers, and containers are shipped via truck. Service includes, loading labor and unloading labor. Excludes packing labor and materials, unpacking labor, and debris removal.|
| `Tier 6` | CONUS |DIY, containerized. Shipping container is dropped at residence, and transferee is responsible for all packing and loading. Packed container is picked up and transported to destination residence. Transferee is responsible for unloading and unpacking. Empty container is picked up when ready. |

## Shipment Resource
A move represents all of the different shipments that person or family needs to be transported. A shipment represents one specific consignment or transportation type of a move. A move has at least one shipment and can have multiple shipments. EX: a move has one air shipment and one fclLoose shipment.

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
The price for a shipment is based on the most recent update and will be returned as a [Charge Details](#charge-details-resource). The first time you try to get prices you will recieve a 400 telling you that you need to post prices first. This will also happen if a certain amount of time has elapsed or if something substantial about the shipment changes.
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
    "status": 400,
    "title": "Bad Request",
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
Awarding a shipment is the act of selecting a specific supplier for a given shipment and lets the supplier know that you are ready for them to start working on the shipment. To award a shipment you need to have a [Charge Details](#charge-details-resource) from the prices route with a price that is not null. Once a shipment is awarded a corresponding [Service](#service-resource) will be created and it is no longer necessary to create a prices request to get pricing. Awarding a shipment is a [Long Running Request](../api_conventions/async.html).
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
If a shipment becomes unnecessary it can be canceled and will no longer be completed by the supplier. Shipments cannot be canceled after packing final stage has been submitted. 
If a shipment is in fact not needed, you can cancel it with the route below. Canceling a shipment is a [Long Running Request](../api_conventions/async.html). The response is a Location to check the status.\
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
An update is a record of all updates made to a shipment. It functions as an audit trail for changes to modes, weight, volume, etc.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `status`|[Status](#status)|(readonly) what stage this update is for|
| `inventoryCount`|number|the number of packed cartons and/or items in the shipment, entered by the supplier only when submitting the pack final stage|
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
A service represents an order that you have placed with a supplier, they are created automatically when you award a shipment.

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
Charge Details provide a detailed record of the pricing of a shipment as it moves through the workflow. Charge Details are created automatically when a shipment is awarded or a supplier completes a [stage](#status). A Charge Details are returned when [Pricing A Shipment](#price-shipment). In order to [Award A Shipment](#award-shipment) the price in the Charge Details cannot be NULL.

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
Metadata are helpful, pieces of information that the user is allowed to store about a [Move](#move-resource) or a [Shipment](#shipment-resource).

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
Throughout any given shipment, information is processed and provided in stages. These stages represent the lifecycle of a move, and are points when prices can be updated.

|Status|Description|
| --------------- | -------- |
| `bid` |Bid stage is shipment creation and used to see what prices are available, to request rates (if necessary), and award the move to a supplier with completed rates|
| `survey`|Survey stage is updated by the awarded supplier with weight, volume, container size (if applicable), and supplemental charges as identified in the physical or virtual pre-move survey|
| `pack` |Pack stage is updated by the awarded supplier with weight, volume, container size (if applicable), and supplemental charges as completed after packing with actual / final shipment weight and volume. This could represent the final price if no other charges occur at the following stages|
| `delivery`|Delivery stage is updated by the awarded supplier with additional supplemental charges that occurred at delivery. Shipment weight and volume could potentially be updated again if a re-weigh was required but is very rare. This could represent the final price if no other charges occur at the following stages|
| `additional`|Additional stage is updated by the awarded supplier with additional supplemental charges that occurred after delivery or were not known at the time of delivery such as demurrage charges or import taxes. This could represent the final price if no other charges occur at the following stage|
| `complete` |Complete stage is updated by the awarded supplier with any remaining supplemental charges or adjustments. For example, on a corporate move if a certain supplemental service was not authorized, that charge could be removed. No other changes to the shipment or the price occur after the complete stage is submitted.|

## Tariff Resource
Tariffs are the fundamental pricing mechanism of moving services. Suppliers enter their prices into PricePoint based on the service markets (locations) and enter prices for all possible weights / volumes based on the selected tariff location and rate rules. 
Multiple tariffs are often needed to price a door-to-door move, and suppliers are able to update their rates at anytime therefore tariff ids are used to identify exactly what tariff was used to price a shipment.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|

## Booker Resource
A booker within the PricePoint system is any supplier / mover that is handling a door-to-door move. Bookers are the suppliers’ door-to-door prices that are shown in Portal, and moves are awarded to bookers.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `id`| number|the unique identifier|
| `name`|string|the name of the supplier|