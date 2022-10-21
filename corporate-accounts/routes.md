# PricePoint Portal API
The PricePoint Portal is a unique platform that allows companies with household goods moves to be serviced to get an instant price, easily manage their supply chain of movers, and book moves. PricePoint Portal simplifies the process so users can get an actual, instant quote; not an estimate, not a range, but an actual price. The Portal provides price security with locked-in pricing on every move throughout all phases of a move. And PricePoint offers movers dynamic pricing so they can change prices for future moves any time as market conditions fluctuate. 

The PricePoint Portal API allows API users to create moves, run quotes, award moves, and see move price updates via the API. This API documentation gives all of the details you need to connect to the PricePoint Portal for International or CONUS (US Domestic). 

All workflows and business logic follows the PricePoint Portal workflow. If you would like more information or a demo, please contact us at hello@pricepointmoves.com. If you have technical questions, please contact dev@pricepointmoves.com.
## Move Workflow Description
1. [Required information](#required-information) - Retrieve all information necessary to create a move
    1. [Retrieve currencies](#retrieve-currencies) - determine the currency for the move
    2. [Select client](#retrieve-client) - select from your list of clients
    3. [Determine locations](#search-locations) - Select the origin and destination for the move
1. [Create a move](#creating-a-move)
    * Origin & Destination locations (Locations API)
        * International requires long-lats and Conus requires Zip3
    * Unique reference number (move ID)
    * Currency type
    * Client ID
1. [Create shipment(s)](#Creating-a-second-shipment). Moves can have multiple shipments, where top level move info is the same and shipment mode / size are different.
    * Shipment mode and size (weight, volume, weight/volume units, container size)
1. [Create a shipment quote](#creating-a-quote-for-a-shipment) - get prices from your movers
    * [Request prices](#creating-rate-requests) if necessary
1. [Update shipment](#creating-a-shipment-update) size or mode
1. [Review prices and award move](#Getting-shipment-prices)

## Required information
### Retrieve currencies
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
### Retrieve client

Request:
```
GET /api/v4/corporateAccounts/{corpId}/clients
Content-Type: application/json
x-api-key: {{ api-key }}
```
Response:
```
{
    "limit": 50,
    "offset": 0,
    "length": 50,
    "data": [
        {
            "id": 1,
            "name": "AECOM",
            "type": "international"
        },
        {
            "id": 2,
            "name": "Alere",
            "type": "international"
        },
    ...
    ]
}
```
### Search locations
Request:
```
POST /api/v2/locations/search
Content-Type: application/json
x-api-key: {{ api-key }}

{
    "type": "lat-long",
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
### Creating a move
A move is the main resource that most of this API is based off of. Creating one requires all the top-level move data as well as origin, destination, and at least one shipment that contains an update.Below is a valid example.\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/moves
Content-Type: application/json
x-api-key: {{ api-key }}
{
  "name": "api-test",
  "type": "international",
  "origin": {
    "type": "market",
    "id": 1918,
    "name": "New York City",
    "country": "USA",
    "display": "USA - New York City"
  },
  "destination": {
    "type": "market",
    "id": 1890,
    "name": "London",
    "country": "United Kingdom",
    "display": "United Kingdom - London"
  },
  "currency": {
    "id": 1
  },
  "client": {
    "id": 790
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
If successful, you will receive the created move id:  

Response:
```
HTTP/1.1 201 Created

{
    "id": 20209
}
```
### Retrieve a move by id
Getting a move is to access already created moves. You will need a corp ID and a move ID (reference number).\
Request:
```
GET /api/v4/corporateAccounts/{corpId}/moves/{moveId}
Content-Type: application/json
x-api-key: {{ api-key }}
```
The response is a top level move object.  

Response:
```
HTTP/1.1 200 OK

{
    "id": 20209,
    "name": "api-demo",
    "type": "international",
    "date": "2021-06-01T17:56:36.000Z"
}
```
You can expand a moves sub resources using the `expand` query param. The below example expands the move's origin and shipments as well as the shipment's updates.\
Request:
```
GET /api/v4/corporateAccounts/{corpId}/moves/{moveId}?expand=origin,shipments&shipments.expand=updates
Content-Type: application/json
x-api-key: {{ api-key }}
```
Response:
```
HTTP/1.1 200 OK

{
    "origin": {
        "id": 1890,
        "name": "London",
        "country": "United Kingdom",
        "type": "market",
        "date": "2019-05-13T15:46:49.000Z",
    },
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
                            "id": 125640,
                            "status": "bid",
                            "date": "2021-06-01T17:56:37.000Z",
                            "inventoryCount": -1
                        }
                    ]
                },
                "id": 27970,
                "name": "Shipment (1)",
                "date": "2021-06-01T17:56:37.000Z"
            }
        ]
    },
    "id": 20209,
    "name": "api-demo",
    "type": "international",
    "date": "2021-06-01T17:56:36.000Z"
}
```

### Creating a quote for a shipment
In order to get the latest prices for a shipment, you first need to generate quotes for all of your bookers with completed pricing. Those quotes are good for 30 days. This is done via a POST as shown below:\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/moves/{moveId}/shipments/{shipmentId}/quotes
Content-Type: application/json
x-api-key: {{ api-key }}

{}
```
Response:
```
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1

{}
```
Generating quotes is an async process. You can check the status of the request via a GET request to the Location header in the response. 
### Checking the status of an async request
Some parts of PricePoint require asynchronous requests because the requests themselves can take time to complete. POST requests that are async and require polling to know when they are done.\
Request:
```
GET /api/v1/requestStatus/{requestStatusId}/status
Content-Type: application/json
x-api-key: {{ api-key }}

{}
```
If the request is still processing, the response will be:  

Response:
```
HTTP/1.1 202 Accepted

{}
```
If the request finished processing successfully, the response will provide the Location of the created resource. 

Response:
```
HTTP/1.1 303 See Other
Location: /v4/corporateAccounts/{corpId}/moves/{moveId}/shipments/{shipmentId}/quotes/{quoteId}

{}
```
If the request encountered an error, then the response will contain the error.

Response: 
```
HTTP/1.1 400 Bad Request

{
	error: 'something went wrong'
}
```
### Getting shipment prices
Within the PricePoint Portal UI, shipment prices are shown in Charge Details where all price breakdowns and details are listed This route returns chargeDetails that contain all the pricing information. One of the chargeDetails returned here will be needed for the /submitStage route.\
Request:
```
GET /api/v4/corporateAccounts/{corpId}/moves/{moveId}/shipments/{shipmentId}/prices
Content-Type: application/json
x-api-key: {{ api-key }}
```
Response:
```
HTTP/1.1 200 OK

{
  "date": "2021-06-02T22:16:05.678Z",
  "currency": {
    "exchangeRate": 1,
    "id": 1,
    "name": "US Dollars",
    "symbol": "USD",
    "priceInUsd": 1
  },
  "lineItems": {
    "data": []
  },
  "price": null,
  "id": "1fb9f4e0-c3f0-11eb-bd3c-67708a596616",
  "currencies": {
    "data": [
      {
        "exchangeRate": 1,
        "id": 1,
        "name": "US Dollars",
        "symbol": "USD",
        "priceInUsd": 1
      }
    ]
  },
  "tariffs": {
    "data": []
  },
  "units": {
    "data": [
      {
        "amount": "2200",
        "unit": "cuft"
      },
      {
        "amount": "15300",
        "unit": "lb"
      },
      {
        "amount": "40",
        "unit": "container"
      }
    ]
  },
  "errors": {
    "data": []
  },
  "modifications": {
    "data": []
  },
  "source": {
    "id": 295959
  },
  "booker": {
    "id": 404,
    "name": "John Mason (BaggageHub)",
    "email": "pricepointtester@gmail.com",
    "internal": false
  }
}

```
### Creating rate requests
Movers are unlikely to have completed rates for all locations worldwide for all shipment types. If a booker does not have completed prices, you can make rate requests to notify them that you would like pricing. You can create rate requests for the provided bookers via the route below.\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/moves/{moveId}/shipments/{shipmentId}/rateRequests
Content-Type: application/json
x-api-key: {{ api-key }}

{
  "bookerIds": [123,456],
  "mode": "air"
}
```
Response:
```
HTTP/1.1 201 Created

{
    "ids": [
        3512,
        3511
    ]
}
```

### Getting rate requests
You can check on the status of a rate request and if complete get the completed date with the following route:\
NOTE: to get updated pricing you will need to generate a new shipment quote.\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/rateRequests/{rateRequestId}?expand=completed
Content-Type: application/json
x-api-key: {{ api-key }}
```
Response:
```
HTTP/1.1 200 OK

{
    "completed": null,
    "id": 43,
    "corporateAccountId": 96,
    "moveId": 18056,
    "moveName": "API Test",
    "originId": 4914,
    "originName": "016 - Worcester, MA",
    "originCountry": "United States",
    "destinationId": 4935,
    "destinationName": "086 - Trenton, NJ",
    "destinationCountry": "United States",
    "shipmentId": 25270,
    "shipmentName": "Shipment (1)",
    "mode": "Tier 2",
    "bookerId": 325,
    "userId": 3371,
    "date": "2021-02-17T23:54:59.000Z"
}
```
If the `completed` field is null, then the request has not been completed. A completed rate request would have `completed` equal an object with the date it was completed.  
Response:
```
HTTP/1.1 200 OK

{
    "completed": {
      "id": 39,
      "rateRequestId": 107,
      "date": "2021-02-18T20:11:30.000Z"
    },
    "id": 43,
    "corporateAccountId": 96,
    "moveId": 18056,
    "moveName": "API Test",
    "originId": 4914,
    "originName": "016 - Worcester, MA",
    "originCountry": "United States",
    "destinationId": 4935,
    "destinationName": "086 - Trenton, NJ",
    "destinationCountry": "United States",
    "shipmentId": 25270,
    "shipmentName": "Shipment (1)",
    "mode": "Tier 2",
    "bookerId": 325,
    "userId": 3371,
    "date": "2021-02-17T23:54:59.000Z"
}
```

### Creating a shipment update
By creating a shipment update you can get any changes that have occurred on a shipment. Updates contain information about units like weights and volumes as well as the mode of the shipment, and the corresponding price updates. The newest update is the current price based on the most recent shipment information.\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/moves/{moveId}/shipments/{shipmentId}/updates
Content-Type: application/json
x-api-key: {{ api-key }}

{
  "modes": {
    "data": [
      "fclLoose"
    ]
  },
  "units": {
    "data": [
      {
        "unit": "cuft",
        "amount": "200",
        "type": "volume"
      },
      {
        "unit": "lb",
        "amount": "1000",
        "type": "weight"
      },
      {
        "unit": "container",
        "amount": "20",
        "type": "container"
      }
    ]
  }
}
```
Response:
```
HTTP/1.1 201 Created

{
    "id": 12345
}
```
### Submitting a shipment stage
Within the PricePoint Portal movers submit stages in order to update shipments. This route is used to progress the shipment to the next stage, Ex: from bid to survey. It takes a chargeDetail object that is generated from the /prices route as the body. The route is async and will thus return headers to check the request status.\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/moves/{moveId}/shipments/{shipmentId}/updates/submitStage
Content-Type: application/json
x-api-key: {{ api-key }}

{
  ...chargeDetails
}
```
Response:
```
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1

{}
```
### Creating a second shipment
A shipment is a child resource of a move which can have N number of shipments. Creating a shipment requires all top-level info as well as at least one valid update.\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/moves/{moveId}/shipments
Content-Type: application/json
x-api-key: {{ api-key }}

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
If successful, you will receive the created shipment id:  
Response:
```
HTTP/1.1 201 Created

{
    "id": 12345
}
```
### Canceling a shipment
If a shipment is in fact not needed, you can cancel it with the route below. The response is a Location to check the status.\
Request:
```
POST /api/v4/corporateAccounts/{corpId}/moves/{moveId}/shipments/{shipmentId}/cancel
Content-Type: application/json
x-api-key: {{ api-key }}

{}
```
Response:
```
HTTP/1.1 202 Accepted
Location: /api/v1/requestStatus/12345/status
Retry-After: 1

{}
```