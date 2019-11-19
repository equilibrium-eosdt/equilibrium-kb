# Delta hedging using EOSDT

A trader is often interested in acquiring a put option on his or her portfolio. This provides protection against market declines while preserving the potential for a gain if the market does well.

One way to do it is to create a synthetic put option on the portfolio by maintaining a position in the underlying asset \(or portfolio\) so that the delta of the position equals the delta of the required put option.

In traditional financial markets, there are two reasons why it may be more attractive for the portfolio holder to create the required put option synthetically than to buy it on the market. First, options markets do not always have the liquidity to absorb the trades required by large fund managers. Second, fund managers often require strike prices and exercise dates that are different from those available in exchange-traded options markets.

In the realm of blockchain there aren’t any mature options or futures markets that currently allow for hedging strategies, so the only way to hedge one’s portfolio is to actually trade it against a “risk-free” asset, by hedging into it at times of market decline and hedging out of it when markets grow. This is where the synthetic option approach outlined above comes into play.

Here’s how the Equilibrium-based system tackles user portfolio hedging with help from EOSDT:

1. Users stake their portfolios to a separate hedging contract. 
2. This contract listens to price changes from the oracle and reacts to them if necessary. 
3. Users specify their hedge period and desired drawdown.
4. The system monitors the portfolio value:
   * As the value of the original portfolio declines, the delta of the put option becomes more negative and the proportion of the original portfolio sold must be increased. 
   * As the value of the original portfolio increases, the delta of the put option becomes less negative and the proportion of the original portfolio sold must be decreased.      

## **Example:**

A portfolio is worth $50,000. To protect against market downturns, the portfolio owner requires a six-month put option on the portfolio with a strike price of $45,000. The risk-free interest rate is 1% and the volatility of the portfolio is 25% per annum. We’re given all the data to create the required option synthetically:

Calculate:

`d = ln(S_0/K) + (R + sigma^2/2)*T / [sigma * sqrt(T)]`

where:

* `S_0` - initial value of the portfolio = $50,000
* `K` - strike price = $45,000
* `R` - risk free interest rate = 1% \(this is a current cost of EOSDT generation in Equilibrium\)
* `Sigma` - annualized portfolio volatility = 25%.
* `T` - hedging period, T = 0.5 years \(6 month\). 

`d = ln(50/45) + (0.01 + 0.25^2/2)*0.5 / [0.25 * sqrt(0.5)] ~ 0.7127`

And the delta of the required option is: `delta = N(d) - 1`, where `N(d)` is the standard normal cumulative distribution function for a specified value

`delta = N(0.7127) - 1 = - 0.2380`

This delta value shows that 23.8% of the portfolio should be sold initially and invested in EOSDT to match the delta of the required option. The amount of the portfolio sold must be monitored frequently. For example, if the value of the portfolio reduces to $47,000 after one day, the delta of the required option changes to -0.3584 and further 12.04% of the original portfolio should be sold and invested in EOSDT. If the value of the portfolio increases to $53,000, the delta of the required option changes to -0.1486 and 8.94% of the original portfolio should be repurchased.

