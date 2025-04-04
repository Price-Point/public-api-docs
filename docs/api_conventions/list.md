---
layout: default
title: Lists
nav_order: 3
parent: Conventions
---
# Lists
{: .no_toc }
## Table of Contents
{: .no_toc .text-delta }
1. TOC
{:toc}
## Pagination
List resources allow both bulk and indexed access, see [URL Structure](url_structure.html) for more details. When accessed via build routes list resources are enclosed in a data envelope that includes pagination information.
#### Pagination Information
{: .no_toc }
Request:
```
GET /api/v1/items
Content-Type: application/json
x-api-key: [api-key]
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
## Query String Parameters
List resources support customizing your request with the following parameters

| Param  | Description |
| --------------- | -------- |
| [fields](#fields)|determine which data is returned by the api|
| [expand](#expand-sub-resources)|retrieve sub resources|
| [limit](#limit)|number of results|
| [offset](#offset)|result to start with|
| [sort](#sorting)|how to order results|

### Array Parameters
{: .no_toc }
1. Query params support sub resources. To set the limit for a sub resource you would set `[sub resource name].limit=1` in the request. The same applies to other query params like `expand`. You can expand the notes metadata shipments by adding `[sub resource name].expand=metadata` to the query params.
1. Query params are interpreted as comma separated lists, allowing users to submit multiple entries for the same parameter. E.G. `expand=notes,foo`

#### Array Parameters
{: .no_toc }
```
GET /api/v1/items/10?expand=notes,foo&notes.limit=1&notes.expand=metadata
```
## Filtering
List values can be filtered to return specific values. You can filter based on a resource's primitive fields. The following example only returns items with the name "foo-bar".
#### With Filter
{: .no_toc }
Request:
```
GET /api/v1/items?name=foo-bar
Content-Type: application/json
x-api-key: [api-key]
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
            "id": 12,
            "name": "foo-bar"
        }
    ]
}
```
## Fields
By default resources return all primitive fields, I.E. complex fields like arrays and objects will need to be [expanded](#expand-sub-resources). It is possible to customize the returned fields using the fields query param.
#### Without Fields
{: .no_toc }
Request:
```
GET /api/v1/items/10
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
HTTP/1.1 200 OK
{
    "id": 10,
    "name": "10th Item"
}
```
#### With Fields
{: .no_toc }
Request:
```
GET /api/v1/items/10?fields=id
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
HTTP/1.1 200 OK
{
    "id": 10
}
```
## Expand Sub Resources
There are 2 ways to access child resources. The first way is to add the child resource name to the [URL path](url_structure.html#example-list-sub-resource). The other method is to use the `expand` query param. This method is useful when you want information about parent and child resources from one request. 

{: .note}
There are 4 well known strings that can also be supplied to expand as a shortcut: `primitive` will include all primitive fields (default), `object` will expand all objects, `array` will expand all arrays, `all` will expand everything.

#### Expand Sub Resource
{: .no_toc }
Request:
```
GET /api/v1/items/10?expand=notes
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
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
                "id": 1,
                "note": "I am a note"
            }
        ]
    }
}
```
## Limit
All lists are paginated. Limit is used to determine the page size of all results returned by the API.

{: .note}
If the limit param is set to `null` all results will be returned with the request, however this should be used with caution and only when the caller knows the size of the list to be small.

#### With Limit
{: .no_toc }
Request:
```
GET /api/v1/items?limit=3
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
HTTP/1.1 200 OK
{
    "limit": 3,
    "offset": 0,
    "length": 3,
    "data": [
        {
            "id": 1,
            "name": "foo"
        },
        {
            "id": 2,
            "name": "bar"
        },
        {
            "id": 3,
            "name": "foo-bar"
        }
    ]
}
```
## offset
All lists are paginated. offset is used to determine where in the results returned by the API to start the page.
#### With offset
{: .no_toc }
Request:
```
GET /api/v1/items?offset=2
Content-Type: application/json
x-api-key: [api-key]
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
            "id": 3,
            "name": "foo-bar"
        }
    ]
}
```
## Sorting
List values can be sorted by their fields in ascending or descending order, `sort` takes two agreements. The first is the field name to be sorted. The second is the order (either `asc` or `desc`). They are separated by a `:`.
#### With Sort
{: .no_toc }
Request:
```
GET /api/v1/items?sort=name:desc
Content-Type: application/json
x-api-key: [api-key]
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