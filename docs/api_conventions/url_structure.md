---
layout: default
parent: Conventions
title: URL Structure
nav_order: 2
---
# URL Structure
{: .no_toc }
## Table of Contents
{: .no_toc .text-delta }
1. TOC
{:toc}
## Resource Path
All PricePoint APIs follow the same format and include a version in the path. The consumer may rely on all updates to an API with the same version remaining backwards compatible. All top level resources can be accessed with the same path structure, `/api/v[version]/[resource]`.
### Top Level Resource
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
## Indexing And Sub Resources
The PricePoint API differentiates between 2 resource types, lists and objects. All top level resources in the PricePoint API are [lists](list.html). All list resource support bulk, `/api/v[version]/[resource]`, and index `/api/v[version]/[resource]/[id]` access. The PricePoint API supports access to sub-resources via indexed parents using `/api/v[version]/[resource]/[id]/[child]`.
#### Index Resource
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
#### List Sub Resource
{: .no_toc }
Request:
```
GET /api/v1/items/10/notes
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
            "id": 1,
            "note": "I am a note"
        }
    ]
}
```
#### Object Sub Resource
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