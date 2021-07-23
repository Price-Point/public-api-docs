# Moves Resources
A description of the resources in the Moves API

## corporateAccount
A corporateAccount is an entity that primarily creates and manages moves.

| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `name`        | string   | the name of the corporate account|
| `clients`        | Client[]   | an array of clients|
| `allowances`        | Allowance[]   | an array of allowances|
| `moves`        | Move[]   | an array of moves|
| `rateRequests`        | RateRequest[]   | an array of rateRequests|

## move
A move contains all the information needed for pricing of a physical move. 

| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `name`        | string   | the name of the move, must be unique|
| `type`        | string   | the type of move. valid fields: ['international', 'conus']|
| `origin`        | Object   | a location object containing origin location data|
| `destination`        | Object   | a location object containing destination location data|
| `client`        | Object   | a client object containing the id of the client|
| `currency`        | Object   | a currency object containing the id of the currency|
| `shipments`        | shipment[]   | an array of shipments|

## shipment
A shipment is a child resource for moves. A shipment represents a part of a physical move. EX: there could be a move with 2 shipments. One air shipment and one fclLoose shipment. 

| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `name`        | string   | the name of the shipment|
| `updates`        | update[]   | an array of updates. the latest update is used for the /prices route|
| `quotes`        | quote[]   | used for pricing at the bid stage|
| `services`        | service[]   | used for pricing after the bid stage|
| `chargeDetails`        | chargeDetail[]   | contains pricing information from past stages|
| `metadata`        | metadata[]   | an array of metadata|

## update
An update contains all the information that can change about a shipment.  

| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `status`        | string   | what stage this update is for. The API automatically sets this|
| `modes`        | string[]   | the modes of the shipment. most common to only have one. valid fields: [fclLoose, fclCased, lcl, air, roadExport, roadBlanket]|
| `units`        | unit[]   | contains the units. I.E. weight, volume, container size information|

## unit
A unit is used to calculate pricing. 

| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `type`        | string   | the type of unit. one of ['weight','volume','container']|
| `amount`        | string   |  always a number except for the special case of a container with size '40hc'|
| `unit`        | string   | one of ['lb','kg','cuft','cbm','container']|

## chargeDetail
A chargeDetail contains all pricing information for a particular stage and booker. These are generated via the /prices route and are stored in a shipment when a stage is complete.  

| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `price`        | number or null   | the overall price |
| `currency`        | currency   | the currency that the price is in|
| `lineItems`        | lineItem[]   | lineItems are an itemized list of all the charges that go into the final price|
| `tariffs`        | tariff[]   | contains all the tariffs used to generate the price|
| `units`        | unit[]   | contains all the units used to generate the price|
| `currencies`        | currency[]   | contains all the currencies used to generate the price|
| `modifications`        | modification[]   | contains any extra discounts or markups in the price|
| `errors`        | string[]   | contains any errors about the chargeDetail|

### Note(s)
1. The price is the sum of lineItems and modifications denominated in `currency`.
1. A chargeDetail is only valid if the price is not null. If the price is null, then check the `errors` field to see why.
## metadata
Multiple resources have metadata like moves and shipments. They all follow the same structure. 

| Field           | Type     | Description                                     |
| -------------   |----------|-------------------------------------------------|
| `name`        | string   | the name: EX: 'allowanceId'|
| `value`        | string   |  the value: EX: '344'|