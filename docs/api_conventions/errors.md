---
layout: default
title: Errors
nav_order: 5
parent: Conventions
---
# Errors
{: .no_toc }
## Table of Contents
{: .no_toc .text-delta }
1. TOC
{:toc}
## Status Codes
The PricePoint API uses standard HTTP status codes to indicate the success or failure of a request. The status codes returned by the PricePoint API include:

| Status Code  | Description |
| --------------- | -------- |
|`200`|The request succeeded|
|`201`|A new entity was created|
|`400`|The request is malformed or missing information|
|`401`|The request is not authenticated|
|`403`|The user is not authorized to issue the request|
|`404`|The requested resource doesn't exist|
|`429`|Rate limit exceeded|
|`500-504`|There is a problem with the PricePoint API, contact support|

## Error Format
In the event of a 4xx or 5xx error the PricePoint API sets the body of the response to the following error structure.

| Field           | Type     | Description                                     |
| --------------- | -------- | ----------------------------------------------- |
| `status`|string|the HTTP status code|
| `title`|string|the description of the status|
| `details`|string|an explanation of the error|
| `instance`|string|the url of the request|

### Error Request
{: .no_toc }
Request:
```json
GET /api/v1/error
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 400 Accepted
{
    "status": 400,
    "title": "Bad Request",
    "details": "Missing field "name"",
    "instance": "https://pricepoint.pricepointmoves.com/api/v1/error"
}
```
