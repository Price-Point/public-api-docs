# Navigation
[General API Information](#general-api-design)  
[Move API](corporate-accounts/routes.md)  
[Move Resources](corporate-accounts/resources.md)  
[Currencies API](currencies.md)  
[Locations API](locations.md)
# General API Design


## 1. Overview
All APIs are JSON-based and can accessed via the endpoint `https://pricepoint.pricepointmoves.com`.   
All request use `https`, not `http`.

## 2. Authentication

All requests need to be authenticated by using the `x-api-key` Header. Contact PricePoint if you would like to use our APIs.

## 3. Accessing Resources
The following access patterns are universal. The below examples will use the corporate accounts API.
### 3.1 Accessing the root resource(s)  
A list of corporate accounts can be accessed like so:   
Request:
```
GET /api/v4/corporateAccounts/
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "limit": 50,
    "offset": 0,
    "length": 50,
    "data": [
        {
            "id": 1,
            "name": "Foo"
        },
        {
            "id": 2,
            "name": "Bar"
        },
        ...
    ]
}
```
#### Note(s):
1. Lists are in data envelopes and provide the limit, offset, and length of the returned data.

Lists can be paginated via the `limit` and `offset` query params. EX:    
Request:
```
GET /api/v4/corporateAccounts/?limit=2&offset=10
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "limit": 2,
    "offset": 10,
    "length": 2,
    "data": [
        {
            "id": 11,
            "name": "Foo"
        },
        {
            "id": 12,
            "name": "Bar"
        }
    ]
}
```

You can access a specific resource by adding its ID in the URL after the resource name. The example below returns the corporate account with the ID of 10.
```
GET /api/v4/corporateAccounts/10
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "id": 10,
    "name": "Foo"
}
```
#### Note(s)
1. Resources by default only return their primitive fields. I.E. complex fields like arrays and objects will need to be expanded.

### 3.2 Accessing Child Resources
There are 2 ways to access child resources. The first way is to add the child resource name to the URL path. The example below returns a list of moves owned by corporate account 123.
Request:
```
GET /api/v4/corporateAccounts/123/moves?limit=1
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "limit": 1,
    "offset": 0,
    "length": 1,
    "data": [
        {
            "id": 12345,
            "corporateAccountId": 123,
            "name": "api-demo",
            "type": "conus",
            "clientId": 767,
            "originId": 4572,
            "originName": "100 - New York City, NY",
            "originCountry": "United States",
            "destinationId": 4893,
            "destinationName": "802 - Denver, CO",
            "destinationCountry": "United States",
            "currencyId": 1,
            "userId": 1234,
            "date": "2021-02-25T15:18:35.000Z"
        }
    ]
}
```
The other method is to use the `expand` query param. This method is useful when you want information about parent and child resources from one request.
```
GET /api/v4/corporateAccounts/123?expand=moves&moves.limit=1
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "moves": {
        "limit": 1,
        "offset": 0,
        "length": 1,
        "data": [
            {
                "id": 12345,
                "corporateAccountId": 123,
                "name": "api-demo",
                "type": "conus",
                "clientId": 767,
                "originId": 4572,
                "originName": "100 - New York City, NY",
                "originCountry": "United States",
                "destinationId": 4893,
                "destinationName": "802 - Denver, CO",
                "destinationCountry": "United States",
                "currencyId": 1,
                "userId": 1234,
                "date": "2021-02-25T15:18:35.000Z"
            }
        ]
    },
    "id": 96,
    "name": "Move Awarder",
    "internal": true
}
```
#### Note(s):
1. query params work recursively. The move limit was set via `moves.limit=1` in the request. The same applies to other query params like `expand`. You can expand the moves shipments by adding `moves.expand=shipments` to the query params.
1. params are comma separated lists. E.X. `expand=moves,clients`

### 3.3 Filtering lists
List values can be filtered to return specific values. You can filter based on a resource's primitive fields. The following example only returns moves with the name "foo-bar".
```
GET /api/v4/corporateAccounts/123/moves?name=foo-bar
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "limit": 50,
    "offset": 0,
    "length": 1,
    "data": [
        {
            "id": 12345,
            "corporateAccountId": 123,
            "name": "foo-bar",
            "type": "conus",
            "clientId": 767,
            "originId": 4572,
            "originName": "100 - New York City, NY",
            "originCountry": "United States",
            "destinationId": 4893,
            "destinationName": "802 - Denver, CO",
            "destinationCountry": "United States",
            "currencyId": 1,
            "userId": 1234,
            "date": "2021-02-25T15:18:35.000Z"
        }
    ]
}
```
### 3.4 Sorting lists
List values can be sorted by their fields in ascending or descing order. The example below sorts moves by their origin name in descing order.
```
GET /api/v4/corporateAccounts/123/moves?sort=originName:desc
Content-Type: application/json
x-api-key: api-key
```
Response:
```
HTTP/1.1 200 OK

{
    "limit": 50,
    "offset": 0,
    "length": 2,
    "data": [
        {
            "id": 567522,
            "corporateAccountId": 123,
            "name": "foo-bar2",
            "type": "conus",
            "clientId": 767,
            "originId": 4572,
            "originName": "802 - Denver, CO",
            "originCountry": "United States",
            "destinationId": 4893,
            "destinationName": "100 - New York City, NY",
            "destinationCountry": "United States",
            "currencyId": 1,
            "userId": 1234,
            "date": "2021-02-25T15:18:35.000Z"
        },
        {
            "id": 12345,
            "corporateAccountId": 123,
            "name": "foo-bar",
            "type": "conus",
            "clientId": 767,
            "originId": 4572,
            "originName": "100 - New York City, NY",
            "originCountry": "United States",
            "destinationId": 4893,
            "destinationName": "802 - Denver, CO",
            "destinationCountry": "United States",
            "currencyId": 1,
            "userId": 1234,
            "date": "2021-02-25T15:18:35.000Z"
        }        
    ]
}
```
#### Note(s):
1. `sort` takes two arguements. The first is the field name to be sorted. The second is the order (either `asc` or `desc`). They are seperated by a `:`.