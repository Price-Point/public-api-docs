---
layout: default
title: Async Requests
nav_order: 6
parent: Conventions
---
# Async Requests
Some requests to the PricePoint API require a considerable time to complete. To avoid leaving open long running HTTP connections the PricePoint API uses an intermediate Status resource. POST requests to long running resources will receive a 202 response code with the Location and Retry-After headers set to appropriate values. When the request is complete the status service will respond with a 303 redirect if successful and an [error](errors.html) if unsuccessful.
### Async Request
{: .no_toc }
Request:
```json
POST /api/v1/longRunning
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
### Incomplete Status
{: .no_toc }
Request:
```json
GET /api/v1/requestStatus/1234/status
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 202 Accepted
```
### Complete Status
{: .no_toc }
Request:
```json
GET /api/v1/requestStatus/1234/status
Content-Type: application/json
x-api-key: [ api-key ]
```
Response:
```json
HTTP/1.1 303 See Other
Location: /api/v1/longRunning/5678
```