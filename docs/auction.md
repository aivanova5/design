## `PUT /auction/<agent_id>`
## `PUT /auction/<bid_id>`

The auction bid `PUT` method allows the addition and modification of bid by device agents.

### Arguments

| Name | Type | Description
| ---- | ---- | -----------
| device_id | text | The device identifier
| constraint_id | text | The constraint identifier
| quantity | real | The bid quantity (in units)
| unit | text | The quantity units
| price | real | The bid price (in $/h.units)
| flexibility | integer | The device flexibility (0 for non, 1 for quantity)
| state | real | The device's current state (in units)

### Returns

| Code | Body | Descsription 
| ---- | ---- | ------------
| 200  | `{"data" : {"bid_id" : "<bid_id>"}}` | The bid update was successful
| 201  | `{"data" : {"bid_id" : "<bid_id>"}}` | The bid insert was successful
| 400  | `{"error" : "parameter <name> value <value> not valid"}` | A request parameter was not valid
| 403  | `{"error" : "agent <agent_id> not valid for <device_id>"}` | The agent is not authorized to bid on behalf of the device
| 404  | `{"error" : "bid <bid_id> is not valid"}` | The bid was not found or not current pending

### Logic
```mermaid
graph LR
  classDef start stroke-width:0px,fill:black,color:white
  classDef success fill:green,stroke-width:0px,color:white
  classDef error fill:red,stroke-width:0px,color:white
  
  enter([PUT]):::start --> valid{valid args?}
  valid --No--> bad_request([400 bad request]):::error
  valid --Yes--> new{valid agent_id?}
  new --Yes--> allowed{valid device_id?}
  allowed --Yes--> insert_bid[[bid_id = auction.insert_bid:args]]
  allowed --No-->forbidden([403 forbidden]):::error
  new --No--> exists{valid bid_id?}
  exists --Yes--> update_bid[[auction.update_bid:bid_id,args]]
  exists --No--> not_found([404 not found]):::error
  update_bid --> OK([200 bid_id]):::success
  insert_bid --> Created([201 bid_id]):::success
```
