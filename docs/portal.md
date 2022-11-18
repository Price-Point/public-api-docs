---
layout: default
title: Portal Workflow
nav_order: 2
---
# PricePoint Portal
{: .no_toc }
## Table of contents
{: .no_toc .text-delta }
1. TOC
{:toc}

## Overview
The PricePoint Portal is a unique platform that allows companies with household goods moves to be serviced to get an instant price, easily manage their supply chain of movers, and book moves. PricePoint Portal simplifies the process so users can get an actual, instant quote; not an estimate, not a range, but an actual price. The Portal provides price security with locked-in pricing on every move throughout all phases of a move. And PricePoint offers movers dynamic pricing so they can change prices for future moves any time as market conditions fluctuate. 

The PricePoint Portal API allows API users to create moves, run quotes, award moves, and see move price updates via the API. This API documentation gives all of the details you need to connect to the PricePoint Portal for International or CONUS (US Domestic). 

All workflows and business logic follows the PricePoint Portal workflow. If you would like more information or a demo, please contact us at hello@pricepointmoves.com. If you have technical questions, please contact dev@pricepointmoves.com.

## Creating A Move
MIRANDA TODO A move is the start of the PricePoint portal workflow. To create a move you need to select a currency, client, origin, destination (talk about search vs pick one from a list here??), allowance (?), initial weight and volume? You can the use the move resource to initiate a move. A move is the main resource that most of this API is based off of. Creating one requires all the top-level move data as well as origin, destination, and at least one shipment that contains an update.Below is a valid example.

## Pricing A Shipment
MIRANDA TODO fix this In order to get the latest prices for a shipment, you first need to generate quotes for all of your bookers with completed pricing. Those quotes are good for 30 days. This is done via a POST as shown below

## Updating A Shipment
MIRANDA TODO

## Awarding A Shipment
MIRANDA TODO you need to use the data from shipment pricing to award a move to your selected supplier

## Checking For Updates From Suppliers
MIRANDA TODO as your suppliers move shipments through the workflow will notify you via email. You can also check for those updates and any time in the api. Reasons for update include.

## Canceling A Shipment
MIRANDA TODO

## Creating A Second Shipment
MIRANDA TODO

## Pricing An Awarded Shipment
MIRANDA TODO