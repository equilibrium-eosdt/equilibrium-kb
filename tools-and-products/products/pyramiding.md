# Pyramiding with EOSDT

By generating stablecoins in response to a user posting collateral, the way Equilibrium operates allows for leverage. Consider this example: given a current collateralization ratio \(СR\) of 130%, when a user deposits X dollars of EOS, the maximum dollar amount of EOSDT he may generate is X / CR. This EOSDT may be then used to buy additional EOS, which can in turn be used as additional collateral to generate more EOSDT — you get the idea!

You might remember the idea of an infinite geometric series from middle school geometry. But do you remember the formula for its sum when the common ratio R is less than one? In our case R = 1 / CR, so the maximum theoretical achievable leverage L is equal to: 1/\(1-1/CR\)

The following table shows the maximum achievable leverage given a level of collateralization:

| Critical collateralization ratio \(CR\) | Maximum possible leverage |
| :--- | :--- |
| 170% | 2.43 |
| 165% | 2.54 |
| 160% | 2.67 |
| 155% | 2.82 |
| 150% | 3.00 |
| 145% | 3.22 |
| 140% | 3.50 |
| 135% | 3.86 |
| 130% | 4.33 |
| 125% | 5.00 |
| 120% | 6.00 |
| 115% | 7.67 |
| 110% | 11.00 |

With current system parameters, users can enjoy a maximum leverage around 4.33. This is of course the theoretical maximum — in reality, the numbers will be lower as it’s unreasonable to collateralize the position around the critical level. There has to be some safety margin above 130%.

From the user’s perspective, the mechanism works the following way:

* The user picks the desired safety margin \(SM\).
* Based on the SM, the user is offered a choice of leverage \(L\) from 1 to L\_max, where L\_max = 1/\(1-1/\(CR + SM\)\).
* The user supplies X amount of EOS.

To achieve desired leverage L, the system needs to buy \(1 - L\) \* X EOS on the exchange. This may be done in several iterations in the loop according to the following algo:

```text
generate X * oracle price / (CR + SM) EOSDT
amount_to_buy = (L-1)*X EOS
while amount_to_buy >= 0
    buy Q EOS on exchange
    deposit Q EOS to eosdtcntract
    generate Q * Oracle price / (CR + SM) EOSDT
    amount_to_buy = amount_to_buy - Q
end
```

## **New eosdtlevrage contract**

All the above logic is implemented within a new contract called eosdtlevrage.

* **Contract tables:**

  **ltvopers table**

  | Parameter | Type | Description |
  | :--- | :--- | :--- |
  | oper\_id | int64 | ID of the operation, PK. |
  | account | float64 | The ratio of EOS collateral to EOST generated for a given position\_id. |
  | amount\_to\_buy | float64 | The amount of EOS necessary to buy in order to achieve desired leverage \(see the algo above\). Changes on every loop iteration. Initially calculated using rates from eosdtoracliz.orarates |
  | received\_from\_dex | float64 | actual amount of EOS received from DEX when generated EOSDT was sold. used in actual leverage calculations. |

* **Contract actions:**

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
        <td style="text-align:left"><b>deposit</b>
          <br />
          <br />deposit EOS to the eosdtlevrage contract, specifying desired leverage
          L and safety margin (SM)</td>
        <td style="text-align:left">leverage(L)
          <br />safety_margin(SM)</td>
        <td style="text-align:left">
          <p>When missing signature of caller.</p>
          <p>
            <br />
          </p>
          <p>When L 1/(1- 1/(critical_ltv + safety_margin).</p>
          <p>
            <br />
          </p>
          <p>When SM= critical_ltv.</p>
          <p>
            <br />
          </p>
          <p>When there is not enough liquidity on DEX on any of the loop iterations.</p>
        </td>
        <td style="text-align:left"><code>cleos push action eosdtcntract margincall &apos;{&quot;leverage&quot;: &quot;2&quot;,&#x201D;safety_margin&#x201D;,&#x201D;0.3&#x201D;}&apos; -p user</code>
        </td>
      </tr>
    </tbody>
  </table>

