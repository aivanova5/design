Agents are associated with each participant and are required to perform the following functions:

1. Agents shall submit orders into the markets.
2. Agents shall respond to changes in market prices.
3. Agents shall submit metering data to the market.
4. Agents shall submit device non-compliance data to the market.

# Ordering strategies

As a general rule optimal strategies require more information than heuristic strategies.

## Optimal strategies

Optimal strategies shall be used to compute the optimal outcome for analysis purposes and shall be used whenever sufficient information is available to submit an optimal order.

### HVAC optimal strategy

TODO

### Hotwater optimal strategy

TODO

### Photovoltaic optimal strategy

TODO

### Battery optimal strategy

TODO

### Charger optimal strategy

TODO

## Heuristic strategies

Heuristics strategies shall be used when there is insufficient data to submit an optimal strategy or when the participant selects the strategy, if the use participant app allows that.

In many cases the heuristic strategy relies on a value $\dot q_{last}$, which represents the last observed power used by the device.  In general, this value is not useful if it is more than about 30 minutes old.  In such cases, it is acceptable for the agent to momentarily enable the device to measure the value of $\dot q_{now}$ directly.  The duration of such a load must be kept minimal to minimize unexpected costs and system disturbance, while considering any potential harm to the device and accuracy of the measurement.

Example worksheets for each strategy are available at https://docs.google.com/spreadsheets/d/1GUpr-_jw4ydExTzR0FsSDmCAWAUfnCFC0vhHAkzNeq4.

### HVAC heuristic strategy

When only the current indoor air temperature $T_{now}$ is available, the HVAC heuristic strategy shall be employed for orders in the MW auction.  The HVAC agent shall not submit orders in the MWh auction.

The HVAC heuristic strategy is described in Hammerstrom et al[^2]. The HVAC heuristic converts the participants comfort preference and current comfort into a bid price and quantity.  When $T_{min} \lt T_{now} \lt T_{max}$ and $P_{stdev}>0$, the order reservation price for demand shall be computed as 

<DIV ALIGN=center>
  $P_{buy} = P_{mean} + 3 ~ K_{HVAC} ~ P_{stdev} ~ \frac{T_{now}-T_{desired}}{T_{limit}-{T_{pref}}}$,
</DIV>

where 
* $P_{mean}$ is the expected average price, 
* $K_{HVAC}$ is the participant's comfort preference setting for the current system mode, 
* $P_{stdev}$ is the expected standard deviation of the price, 
* $T_{now}$ is the observed temperature, 
* $T_{desired}$ is the preferred temperature, 
* $T_{limit}$ is the minimum desired temperature $T_{min}$ when in heating mode or the maximum desired temperature $T_{max}$ when in cooling mode.

The scale of $K_{HVAC}$ shall be interpreted as follows:
* $K_{HVAC} = 0.0$ means the participant seeks maximum savings.
* $K_{HVAC} \to \infty$ means the participant seeks maximum comfort.
* $K_{HVAC} \approx 1.0$ means the participant seeks a balance between comfort and savings.

The following conditions shall be applied to the input values:
* The value of $T_{limit}$ shall selected based on the last observed rate of change of observed temperature when the system was off, i.e., heating with $\dot T_{now} \lt 0$ and cooling when $\dot T_{now} \gt 0$.
* When $T_{now} \le T_{min}$ then $P_{buy} = P_{min}$. 
* When $T_{now} \ge T_{max}$ then $P_{buy} = P_{max}$.
* When the $P_{stdev} = 0$, then $P_{stdev} = P_{mean}/1000$.

The following conditions shall be applied to the output value:
* If $P_{buy} \lt P_{min}$, then the order shall be submitted as $P_{buy} = P_{min}$.
* If $P_{buy} \gt P_{max}$, then the order shall be submitted as $P_{buy} = P_{max}$.

The order quantity is the most recently observed power demand $Q_{now} = Q_{last}$.

**Table 1: HVAC order data**

| Data | Source | Database table | Unit | Description
| ---- | ------ | -------------- | ---- | -----------
| $P_{mean}$ | Auction | `auctions` | `$/MW.h` | Mean expected price for the next market clearing 
| $P_{stdev}$ | Auction | `auctions` | `$/MW.h` | Standard deviation of price for the next market clearing
| $P_{min}$ | Operator | `resources` | `$/MW.h` | Minimum allowed price
| $P_{max}$ | Operator | `resources` | `$/MW.h` | Maximum allowed price
| $T_{now}$ | Device | `meters` | `degF` | Current temperature in the home
| $T_{last}$ | Device | `meters` | `degF` | Last temperature in the home (used to compute $\dot T_{now}$)
| $Q_{last}$ | Device | `meters` | `MW` | Last device load measurement
| $T_{desired}$ | Participant | `settings` | `degF` | Participant's preferred home temperature
| $K_{HVAC}$ | Participant | `settings` | None | Participant's comfort preference
| $T_{min}$ | Participant | `settings` | `degF` | Participant's minimum acceptable temperature
| $T_{max}$ | Participant | `settings` | `degF` | Participant's maximum acceptable temperature
| $P_{buy}$ | Agent | `orders` | `$/MW.h` | Buy order price
| $Q_{buy}$ | Agent | `orders` | `MW` | Buy order quantity

### Hotwater heuristic strategy

When the agent is unable to determine the temperature of the hotwater in the tank, then the agent shall use the following heuristic strategy.

The hotwater heater heuristic strategy is similar to that of the HVAC system except that being unable to observe the water temperature, the strategy is chosen based on the whether coil is on.  To determine whether the water heater is on, the relay must be closed momentarily and $Q_{now}$ measured directly. If $Q_{now}=0$, then the no order shall be submitted.  If there is a power draw then the measured value $Q_{now}$ is used for the order such that $Q_{buy} = Q_{now}$, and the following order price is used:
<div align=center>
  $P_{buy} = P_{mean}-3*K_{HW}*P_{stdev}*[N(0,1)-0.5]$
</div>

where 
* $P_{mean}$ is the expected average price, 
* $K_{HW}$ is the participant's comfort preference setting for the hotwater heater, 
* $P_{stdev}$ is the expected standard deviation of the price, and
* $N(\mu,\sigma)$ is the normal distribution probability density function with mean $\mu$ and variance $\sigma^2$.

The scale of $K_{HW}$ shall be interpreted as follows:
* $K_{HW} = 0.0$ means the participant seeks maximum savings.
* $K_{HW} \to \infty$ means the participant seeks maximum comfort.
* $K_{HW} \approx 1.0$ means the participant seeks a balance between comfort and savings.

**Table 2: Hotwater order data**

| Data | Source | Database table | Unit | Description
| ---- | ------ | -------------- | ---- | -----------
| $Q_{now}$ | Device | `devices` | MW | The measured power required by the hotwater heater
| $P_{mean}$ | Auction | `auctions` | `$/MW.h` | Mean expected price for the next market clearing 
| $P_{stdev}$ | Auction | `auctions` | `$/MW.h` | Standard deviation of price for the next market clearing
| $K_{HW}$ | Participant | `settings` | None | Participant's comfort preference
| $P_{buy}$ | Agent | `orders` | `$/MW.h` | Buy order price
| $Q_{buy}$ | Agent | `orders` | `MW` | Buy order quantity

### Photovoltaic heuristic strategy

The rooftop PV order strategy is to be a price taker for any non-negative clearing price in the MW auction. The PV agent shall not submit bids in the MWh auction. Therefore, we have

<DIV ALIGN=center>
  $P_{sell} = 0$ and $Q_{sell} = Q_{last}$
</DIV>

for all cases when $Q_{last}>0$.

**Table 3: PV order data**

| Data | Source | Database table | Unit | Description
| ---- | ------ | -------------- | ---- | -----------
| $Q_{last}$ | Device | `meters` | `MW` | Last device load measurement
| $P_{sell}$ | Agent | `orders` | `$/MW.h` | Sell order price
| $Q_{sell}$ | Agent | `orders` | `MW` | Buy order quantity

### Battery heuristic strategy

The stationary battery order strategy is based on two competing objectives. The first objective to bring the battery to the consumer's desired state of charge $Q_{desired}$.  This order is placed in the power capacity (MW) market.  The second objective is to make the battery available to the utility as a energy storage resource which if needed deviates the battery state of charge away from the desired state of charge. This order is placed in the energy storage (MWh) market.

The power order quantity is $\dot Q_{order} = \dot Q_{rated}$ when $Q_{last} < Q_{desired}$ and $\dot Q{order} = - \dot Q_{rated}$ when $Q_{last} > Q_{desired}$.  When $Q_{last} = Q_{desired}$ no power order is submitted.  The power order price is computed as 
<div align=center>
  $P_{order} = N^{-1}[P_{mean},3~K_{ES}~P_{stdev}]\left(\frac{Q_{last}-Q_{min}}{2(Q_{desired}-Q_{min}}\right)$
</div>
when $Q_{min} \lt Q_{last} \lt Q_{desired}$,
<div align=center>
  $P_{order} = N^{-1}[P_{mean},3~K_{ES}~P_{stdev}]\left(\frac{Q_{max}-Q_{last}}{2(Q_{max}-Q_{desired}}\right)$
</div>
when $Q_{desired} \lt Q_{last} \lt Q_{max}$,
<div align=center>
  $P_{order} = P_{max}$
</div>
when $Q_{last} \le Q_{min}$, and 
<div align=center>
  $P_{order} = P_{min}$
</div>
when $Q_{last} \ge Q_{max}$.

TODO: describe the energy storage order heuristic.

**Table 4: Battery order data**

| Data | Source | Database table | Unit | Description
| ---- | ------ | -------------- | ---- | -----------
| $P_{mean}$ | Auction | `auctions` | `$/MW.h` | Mean expected price for the next market clearing 
| $P_{stdev}$ | Auction | `auctions` | `$/MW.h` | Standard deviation of price for the next market clearing
| $P_{min}$ | Operator | `resources` | `$/MW.h` | Minimum allowed price
| $P_{max}$ | Operator | `resources` | `$/MW.h` | Maximum allowed price
| $t_{interval}$ | Operator | `resources` | seconds | Market interval
| $Q_{last}$ | Device | `meters` | `MW` | Last device load measurement
| $Q_{desired}$ | Participant | `settings` | `degF` | Participant's preferred battery state of charge
| $K_{ES}$ | Participant | `settings` | None | Participant's comfort preference
| $P_{buy}$ | Agent | `orders` | `$/MW.h` | Buy order price
| $Q_{buy}$ | Agent | `orders` | `MW` | Buy order quantity

### Charger heuristic strategy

The vehicle chargers heuristic order strategy shall be used when the following information is available from participants:

* $t_{done}$: Time at which full charge is desired.
* $q_{desired}$: State of charge desired at $t_{done}$.
* $K_{EV}$: Aggressiveness in pursuit of goal to reach $q_{desired}$ at $t_{done}$.

Additional information needed is:

* $S_{now}$: Current state of charge

The vehicle charger strategy is described by Behboodi et al[^1]. The buy price heuristic strategy is
<DIV ALIGN=center>
  $P_{buy} = P_{mean} + K_{EV} ~ P_{stdev} ~ \frac{t_{needed}}{t_{available}}$
</DIV>
where $t_{needed} = \frac{q_{desired}-q_{now}}{\dot q_{charge}}$ and $t_{available} = t_{done} - t$. 

When $q_{now} \lt 0.8 ~ q_{max}$, the value of $\dot q_{charge} = \dot q_{rated}$.  However, when $q_{now} \ge 0.8 ~ q_{max}$, then we use
$\dot q_{charge} = \dot q_{rated} ~ \frac{q_{max}-q_{now}}{0.2}$.  Note that when $q_{max}$ is unknown, then it is acceptable to use $\dot q_{charge} = \dot q_{last}$. 

If $p_{buy} \gt p_{max}$, then $p_{buy} = p_{max}$. 

If $p_{buy} \lt p_{min}$, then $p_{buy} = p_{min}$.

# Response strategies

In all cases, the response to a new price $P_{now}$ shall be the compare the price to the original order reservation price. 

For inflexible demand, the power shall be $Q_{now} = Q_{bid}$ when $P_{now} \le P_{buy}$, and $Q_{now} = 0$ otherwise.

For inflexible supply, the power shall be $Q_{now} = -Q_{bid}$ when $P_{now} \ge P_{sell}$, and $Q_{now} = 0$ otherwise.

If the device is flexible, the if $P_{now} = P_{\\\{buy,sell\\\}}$, then the power shall be $Q_{now} = Q_{marginal}$.

# Metering

TODO

# Non-Compliance

Any error in power dispatch less than 1kW or energy dispatch less than 1kWh shall be ignored.

Errors of 1kW or greater shall be charged a penalty based on the difference between dispatched and measured load at the clearing price.

TODO: determine whether we should charge extra for error that cause a price change.

# References

[^1]: Behboodi et al, "Electric Vehicle Participation in Transactive Power Systems Using Real-Time Reltail Prices," in procs. of 2016 48 Hawaii International Conference on System Sciences, https://ieeexplore.ieee.org/abstract/document/7427483.

[^2]: Hammerstrom et al, "Pacific Northwest GridWise Testbed Demonstration Project Part 1: Olympic Peninsula Project," PNNL Report No. 17167, October 2007, https://www.pnnl.gov/main/publications/external/technical_reports/PNNL-17167.pdf.

