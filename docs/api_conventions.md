---
layout: default
title: API Conventions
nav_order: 2
---
# API Conventions
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

## 1. General
Access to all PricePoint APIs is encrypted and uses JSON for all data exchange, APIs can be accessed via the endpoint `https://pricepoint.pricepointmoves.com`.

## 2. Authentication

All requests need to be authenticated by using the `x-api-key` Header. Contact PricePoint at [hello@pricepointmoves.com](mailto:hello@pricepointmoves.com) if you would like to use our APIs.

## 3. Accessing Resources
The following access patterns are applicable to all API endpoints.
### 3.1 Accessing the root resource(s)  
A list of items can be accessed like so:   
Request:
```json
GET /api/v1/items
Content-Type: application/json
x-api-key: {{api-key}}
```
Response:
```json
HTTP/1.1 200 OK

{
    "limit": 50,
    "offset": 0,
    "length": 2,
    "data": [
        {
            "id": 1,
            "name": "Foo"
        },
        {
            "id": 2,
            "name": "Bar"
        },
    ]
}
```
#### Note(s):
{: .no_toc }
1. Lists are in data envelopes and provide the limit, offset, and length of the returned data.

Lists can be paginated via the `limit` and `offset` query params. EX:    
Request:
```json
GET /api/v1/items?limit=2&offset=10
Content-Type: application/json
x-api-key: {{api-key}}
```
Response:
```json
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

You can index into a list of resource to access a specific resource by adding its ID to the URL after the resource name. The example below returns the item with ID 10.
```json
GET /api/v1/items/10
Content-Type: application/json
x-api-key: {{api-key}}
```
Response:
```json
HTTP/1.1 200 OK

{
    "id": 10,
    "name": "Foo"
}
```
#### Note(s)
{: .no_toc }
1. Resources by default only return their primitive fields. I.E. complex fields like arrays and objects will need to be expanded.

### 3.2 Accessing Child Resources
There are 2 ways to access child resources. The first way is to add the child resource name to the URL path. The example below returns a list of notes associated to item 10
Request:
```json
GET /api/v1/items/10/notes
Content-Type: application/json
x-api-key: {{api-key}}
```
Response:
```json
HTTP/1.1 200 OK

{
    "limit": 50,
    "offset": 0,
    "length": 1,
    "data": [
        {
            "id": 12345,
            "note": "I am a note"
        }
    ]
}
```
The other method is to use the `expand` query param. This method is useful when you want information about parent and child resources from one request.
```json
GET /api/v1/items/10?expand=notes
Content-Type: application/json
x-api-key: {{api-key}}
```
Response:
```json
HTTP/1.1 200 OK

{
    "id": 10,
    "name": "Foo"
    "notes": {
        "limit": 50,
        "offset": 0,
        "length": 1,
        "data": [
            {
                "id": 12345,
                "note": "I am a note"
            }
        ]
    }
}
```
#### Note(s):
{: .no_toc }
1. query params work recursively. To set the limit for notes you would set `notes.limit=1` in the request. The same applies to other query params like `expand`. You can expand the notes metadata shipments by adding `notes.expand=metadata` to the query params.
1. params are comma separated lists. E.X. `expand=notes,foo`
1. Example request `GET /api/v1/items/10?expand=notes,foo&notes.limit=1&notes.expand=metadata`

### 3.3 Filtering lists
List values can be filtered to return specific values. You can filter based on a resource's primitive fields. The following example only returns items with the name "foo-bar".
```json
GET /api/v1/items?name=foo-bar
Content-Type: application/json
x-api-key: {{api-key}}
```
Response:
```json
HTTP/1.1 200 OK

{
    "limit": 50,
    "offset": 0,
    "length": 1,
    "data": [
        {
            "id": 12,
            "name": "foo-bar"
        }
    ]
}
```
### 3.4 Sorting lists
List values can be sorted by their fields in ascending or descending order. The example below sorts moves by their origin name in descending order.
```json
GET /api/v1/items?sort=name:desc
Content-Type: application/json
x-api-key: {{api-key}}
```
Response:
```json
HTTP/1.1 200 OK

{
    "limit": 50,
    "offset": 0,
    "length": 2,
    "data": [
         {
            "id": 2,
            "name": "Bbb"
        },
        {
            "id": 1,
            "name": "Aaa"
        }       
    ]
}
```
#### Note(s):
{: .no_toc }
1. `sort` takes two agreements. The first is the field name to be sorted. The second is the order (either `asc` or `desc`). They are separated by a `:`.