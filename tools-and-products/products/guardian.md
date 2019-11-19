# On-chain guardian contract

The idea behind the on-chain guardian is straightforward: on-chain liquidation mechanics significantly speeds up the entire process of buying out the liquidated collateral, as well as burning of EOSDT “bad debt” on the liquidator smart contract. In order to achieve this, we’ve made some amendments to our contract logic. Let’s get into the specifics:

## **Updates to positions contract related to on-chain liquidations**

* **New table** [**ltvratios**](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100&table=ltvratios) **on eosdtcntract**

  The entries \(table rows\) in this table are updated every time someone interacts with EOSDT smart contracts by calling some action or transferring tokens to the system. The table contains the most up-to-date information on all position collateralization ratios.

  | Parameter | Type | Description |
  | :--- | :--- | :--- |
  | position\_id | int64 | ID of user position in Equilibrium framework on the main smart contract. |
  | ltv\_ratio | float64 | The ratio of EOS collateral to EOST generated for given position\_id. |

* **Refactored eosdtcntract margincall actions.**

  <table>
    <thead>
      <tr>
        <th style="text-align:left">Action</th>
        <th style="text-align:left">Parameters</th>
        <th style="text-align:left">Rejections</th>
        <th style="text-align:left">Example</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="text-align:left"><b>margincall</b>
          <br />
          <br />liquidates a position for given position_id</td>
        <td style="text-align:left">position_id (type int64)
          <br />
          <br />the id of the position to liquidate.</td>
        <td style="text-align:left">
          <p>When position_id does not exist.</p>
          <p>When for given position_id ltv_ratio &gt; critical_ltv ratio - it can&#x2019;t
            liquidate a good position.</p>
          <p>When there are no positions to liquidate. (No entries in ltvratios table
            with ltv_ratio below critical for current price).</p>
        </td>
        <td style="text-align:left"><code>cleos push action eosdtcntract margincall &apos;{&quot;position_id&quot;: &quot;30&quot;}&apos; -p user</code>
        </td>
      </tr>
      <tr>
        <td style="text-align:left"><b>margincalpos</b>
          <br />
          <br />liquidates multiple positions by sorting the ltvratios table in order
          of ascending ltv_ratio.</td>
        <td style="text-align:left">N/A
          <br />
          <br />
        </td>
        <td style="text-align:left">When there are no positions to liquidate. (No entries in ltvratios table
          with ltv_ratio below critical for current price).</td>
        <td style="text-align:left"><code>cleos push action eosdtcntract margincallpos -p user</code>
        </td>
      </tr>
      <tr>
        <td style="text-align:left"><b>margincalldeffer</b>
          <br />
          <br />an internal method that gets called when the oracle service calls our
          contract with a rates update. If there are positions to be liquidated,
          deferred margin calls will be created and positions will be liquidated
          in the next blockchain transaction.</td>
        <td style="text-align:left">N/A
          <br />
          <br />
        </td>
        <td style="text-align:left"></td>
        <td style="text-align:left">N/A</td>
      </tr>
    </tbody>
  </table>

## **A new guardian contract**

We initially intended to make this contract a liquidity pool, as in people supply EOSDT or EOS to the guardian contract, which constantly monitors the state of the liquidator. If there are non-zero balances on the liquidator, the guardian performs the necessary actions to buy out non-zero balances from liquidator.

But to speed up the process and give our community something useful early on, we decided to provide a basic functionality: the contract will accept EOSDT transfers, will try to liquidate bad debt, then go to exchanges to convert purchased collateral back into EOSDT, returning it \(and the profit\) to the caller.

The guardian contract is pretty simple under the hood:

```text
if EOSDT is transferred 
  if bad debt on eosdtliqdatr > 0 
    transfer EOSDT to liquidator (collateral auction)
    receive liquidated EOS  
    go to DEX, sell EOS for EOSDT   
    EOSDT received >= EOSDT paid? YES: proceed. NO: revert
  end
end
```

From the user’s perspective, the process looks like transferring EOSDT to the smart contract and receiving the transferred amount back \(assuming there’s no “bad debt” to buy out from liquidator, or there wasn’t enough liquidity on the DEX to convert purchased EOS collateral back into EOSDT\) or more EOSDT.

We will update the guardian contract to act as a liquidity pool for performing liquidations in the nearest future.

As a friendly reminder, the [liquidator contract](https://bloks.io/account/eosdtliqdatr?loadContract=true&tab=Tables&account=eosdtliqdatr&scope=eosdtliqdatr&limit=100) works as follows. All interactions with liquidator contract are done via transfers from any EOS account:

* Transfer EOSDT when there is a non-zero amount of "bad debt" in the liquidator contract and get EOS collateral at a 3% discount.
* Transfer NUT when nut\_collat\_balance is non-zero to buy out excess EOS collateral at a 6% discount.
* Transfer EOS when surplus\_debt is non-zero \(non-zero equilibrium fee, which is not the case right now\) on the liquidator contract to buy out EOSDT at a 3% discount.

## **New guardian.js bot**

Thanks to on-chain margin calls and the new guardian contract, interacting with the liquidator contract has never been easier: guardian.js is now basically a script that handles user accounts and transfers EOSDT to the guardian contract.

