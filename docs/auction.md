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

| Code | Body
| ---- | ------------
| 200  | `{"bid_id" : <str>}`
| 404  | `{"error" : <str>}`

### Logic
```mermaid
graph LR
  enter([PUT]) --> valid{valid args?}
  valid --No--> bad_request([400 bad request])
  valid --Yes--> new{valid agent_id?}
  new --Yes--> allowed{valid device_id?}
  allowed --Yes--> insert_bid[[bid_id = add_bid:args]]
  allowed --No-->forbidden([403 forbidden])
  new --No--> exists{valid bid_id?}
  exists --Yes--> update_bid[[set_bid:bid_id,args]]
  exists --No--> not_found([404 not found])
  update_bid --> OK([200 bid_id])
  insert_bid --> Created([201 bid_id])
```
