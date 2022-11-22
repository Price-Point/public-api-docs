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
A [move](references/corporate_accounts.html#move-resource) is the start of the PricePoint Portal workflow. To create a move you need to select a [name](references/corporate_accounts.html#move-resource), [currency](references/currencies.md#currency-resource), [origins](references/locations.md#location-search), [destinations](references/locations.md#location-search), [client](references/corporate_accounts.html#client-resource), [modes](references/corporate_accounts.html#modes), and [units](references/corporate_accounts.html#unit-resource). All other operations as part of the PricePoint Portal workflow require a move to exist.
### Select A Name (Reference Number)
The [name](references/corporate_accounts.html#move-resource), often referred to as the Reference Number, of a move is the unique identifier that we use within PricePoint. We currently do not capture any PII within our system and the name is used to refer to the move both within and outside of PricePoint. It is best if this number is what would also be reported to the booked mover and is often required to be included on the final invoice. Because we do not use this as PII, transferee names should be discouraged from being used and only alpha-numeric characters should be used, with the dash (-) being allowed.  
### Select Currency
All [prices](references/corporate_accounts.html#charge-details-resource) for this move will be in terms of the currency selected. All tariffs entered by movers will be updated converted to this currency based on the selected currencies exchange rates. Currency exchange rates are updated daily within PricePoint based on a middle market rate. The Currency selected on a given move should be the ultimate currency that the booked mover submits their invoice into the customer. This allows for easy and clear invoice creation and approval. 
### Select Origin And Destination
The origin and destination [locations](references/locations.html#location-resource) are the [market](references/locations.html#market) or [zip3](references/locations.html#zip3) of the transferee’s current home (origin) and home they are moving to (destination). The [Location Search](references/locations.html#location-search) endpoint is the recommended way to determine origins and destinations for a move, to ensure that you do not incur additional supplemental charges you may wish to remove any locations that include [Extra Mileage](references/locations.html#location-with-mileage)
### Select Client
The [client](references/corporate_accounts.html#client-resource) field in a move is used to determine the referral fees, if any, to be added to a move. Additionally, they may be used to track data by the consumers end client. E.g. to track and specify the different department or internal unit that is responsible for the move. Clients and their respective referral fees are set up by PricePoint Admin during the implementation process.
### Create Shipment
At least one [shipment](references/corporate_accounts.html#shipment-resource) is required to create a move. Shipments determine the services to be provided during a move. To create a shipment you must select the services you wish to purchase as well as provide the weight, volume, and other specifics about those services. Those specifics are provided through a shipment's [updates](references/corporate_accounts.html#update-resource).
#### Using An Allowance
[Allowances](references/corporate_accounts.html#allowance-resource) provide a shortcut to determining the [modes](references/corporate_accounts.html#modes) and [units](references/corporate_accounts.html#unit-resource) necessary to create a shipment.
#### Without An Allowance
Select the [service(s)](references/corporate_accounts.html#modes) you want to be a part of this shipment and the [units](references/corporate_accounts.html#unit-resource) you wish to use as input to create a quote. At least a weight and volume unit should be present, for international FCL shipments you should also include a container size (20, 40, 40hc).
## Pricing A Shipment
In order to get the latest prices for a shipment you first need to generate a [quote](references/corporate_accounts.html#quote-resource). The current [price](references/corporate_accounts.thml#price-shipment) for a shipment at the [bid](references/corporate_accounts.html#status) stage is based on the most recent quote and the last [update](references/corporate_accounts.html#update-resource), for any stage passed bid it is based on the ordered service and attempting to create a quote will result in an error.

## Updating A Shipment
[Updates](references/corporate_accounts.html#update-resource) are used to track the history of the changes to the [services](references/corporate_accounts.html#modes) and [units](references/corporate_accounts.html#unit-resource) used to calculate a price. Updates to a shipment during the [bid](references/corporate_accounts.html#status) stage allow you to get [quotes](references/corporate_accounts.html#quote-resource) and [prices](references/corporate_accounts.thml#price-shipment) for different services. Once a shipment is [awarded](#awarding-a-shipment) the updates will come from your selected [supplier](#what-is-a-booker).

{: .note }
If all that has changed are the [units](references/corporate_accounts.html#unit-resource) the [price](references/corporate_accounts.thml#price-shipment) endpoint will automatically reflect those changes, but if the [modes](references/corporate_accounts.html#modes) change it is necessary to run a new [quote](references/corporate_accounts.html#quote-resource) before the new services will be reflected in the price.
## Requesting Rates From A Supplier
[Bookers](#what-is-a-booker) are encouraged to keep as many rates as possible in the system, but there are always going to be origin and destination pairs that cannot be accounted for ahead of time. When a booker doesn’t have a valid [price](references/corporate_accounts.thml#price-shipment), that just means they are missing one or more tariffs for this shipment. The [rate request](references/corporate_accounts.md#rate-requests-for-move) functionality can be used to prompt a supplier to enter rates for a shipment. Once the requested booker has complete rates, you will receive an automated email that rates are complete and they will be available by running a new [quote](references/corporate_accounts.html#quote-resource). 

## Awarding A Shipment
Awarding a shipment lets your selected [supplier](#what-is-a-booker) know you want them to deliver those services. To award a shipment you must select a valid [price](references/corporate_accounts.thml#price-shipment) and pass that to the [submitStage](references/corporate_accounts.html#award-shipment) endpoint.

After the shipments are awarded to a booker, the remaining steps are in the booker’s hands to continue through the process. Each step of a move we call [stages](references/corporate_accounts.md#status). Each stage follows the traditional move process. The bid stage is what you just worked through to get a quote and award the move. As the shipment moves through the workflow you can see pricing for submitted stages reflected is the [charge details](references/corporate_accounts.md#charge-details-resource) for that stage, see the latest [Updates](references/corporate_accounts.html#update-resource), and [price](references/corporate_accounts.thml#price-shipment) for the shipment.

## Canceling A Shipment
If it becomes necessary to cancel a shipment the [cancelShipment](references/corporate_accounts.html#cancel-shipment) route is used. The canceled status is reflected in the shipments [Updates](references/corporate_accounts.html#update-resource) and a final [charge details](references/corporate_accounts.md#charge-details-resource) is recorded for the shipment. Once a shipment is canceled it is no longer worked on by your supplier and cannot be updated further.

## Creating Additional Shipments
In the event that a transferee requires more than one services, e.g. some goods shipped by air and some by ocean, additional shipments may be created for a move by a POST to the [shipment](references/corporate_accounts.html#shipment-resource) resource. The requirements for creating the additional shipment are the same as the [first](#create-shipment).

## What Is A Booker? 
Bookers are your preferred movers. These are movers you have relationships with and you may manage them directly or indirectly as part of your relationship with PricePoint. Bookers are the movers that will handle and oversee the door to door service of this move and all shipments included. Bookers may handle packing, transportation, or delivery services themselves, or they may use their partners or agents to handle certain aspects, but the booker is the mover who oversees all parts of the HHGs move.  