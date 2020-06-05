# Rates contract and rates subscription

The Equilibrium framework works with Oraclize \(now called Provable\), Delphi Oracle, and Liquid Apps oracles to obtain timely market data on-chain. The system uses three different oracles for redundancy and calculates median rate based on the data provided by these oracles. The median value is a final reference price used in all on-chain calculations.

{% hint style="info" %}
Any third party account or smart contract may subscribe to the price feed to receive timely updates required for business logic. It is as simple as staking NUT tokens and implementing public method ratesupdate\(\).
{% endhint %}

Getting rates from Provable and Delphi Oracle works in a similar fashion: each time the system calls its reinit\(\) function \(basically on any user action\), the position contract checks to see if there are any active queries needing collateral and utility token rates, and the rates contract calls for an update of quotes if there are.The contract will check the age of any active query. If it’s more than one minute old, the query gets timestamped and its hash is set to zero. A new query is made by calling the refreshutil\(\) function.

If there are no active queries needing price information, the system creates a deferred action that will call rates with refreshutil\(\) function. The refresh function calls the oracle service and returns price data, which is then amended by a blockchain timestamp. If the last timestamp is more than one hour old, refreshutil\(\) is automatically called again. If the last timestamp is more than six hours old, the rates contract method will throw an exception.

The Liquid Apps solution works slightly differently. The key points are:

* Equilibrium has its own DSP node and the fastest subscription tier from Liquid Apps. 
* Our contract calls a delayed CRON action with Liquid Apps \(DSP will call our contract in some time\).
* In every delayed action, the rates contract asks DSP for new rates via CRON.
* The rates contract updates rates and timestamps from Liquid Apps. 
* An update triggers our reference price \(median\) recalculation.
* Changes in the reference price trigger notifications for subscribers \(like margin calls on the positions contract\)

