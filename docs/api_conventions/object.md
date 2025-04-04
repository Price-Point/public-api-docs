---
layout: default
title: Objects
nav_order: 4
parent: Conventions
---
# Objects
{: .no_toc }
## Table of Contents
{: .no_toc .text-delta }
1. TOC
{:toc}
## Access
Object resources allow access only through direct reference to their name, see [URL Structure](url_structure.html) for more details.
### Object Request
{: .no_toc }
Request:
```
GET /api/v1/items/10/creator
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
HTTP/1.1 200 OK
{
    "id": 1,
    "name": "Jane Doe"
}
```
## Query String Parameters
List resources support customizing your request with the following parameter

| Param  | Description |
| --------------- | -------- |
| [fields](#fields)|determine which data is returned by the api|
| [expand](#expand-sub-resources)|retrieve sub resources|

### Array Parameters
{: .no_toc }
1. Query params support sub resources. To set the limit for a sub resource you would set `[sub resource name].limit=1` in the request. The same applies to other query params like `expand`. You can expand the notes metadata shipments by adding `[sub resource name].expand=metadata` to the query params.
1. Query params are interpreted as comma separated lists, allowing users to submit multiple entries for the same parameter. E.G. `expand=notes,foo`

#### Array Parameters
{: .no_toc }
```
GET /api/v1/items/10?expand=notes,foo&notes.limit=1&notes.expand=metadata
```
## Fields
By default resources return all primitive fields, I.E. complex fields like arrays and objects will need to be [expanded](#expand-sub-resources). It is possible to customize the returned fields using the fields query param.
#### Without Fields
{: .no_toc }
Request:
```
GET /api/v1/items/10/creator
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
HTTP/1.1 200 OK
{
    "id": 1,
    "name": "Jane Doe"
}
```
#### With Fields
{: .no_toc }
Request:
```
GET /api/v1/items/10/creator?fields=id
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
HTTP/1.1 200 OK
{
    "id": 1
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
GET /api/v1/items/10/creator?expand=titles
Content-Type: application/json
x-api-key: [api-key]
```
Response:
```
HTTP/1.1 200 OK
{
    "id": 10,
    "name": "Foo"
    "titles": {
        "limit": 50,
        "offset": 0,
        "length": 1,
        "data": [
            {
                "id": 1,
                "name": "I am a title"
            }
        ]
    }
}
```