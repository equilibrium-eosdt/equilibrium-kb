# Equilibrium Infrastructure: Collateral Adjustment Mechanisms

![](../../.gitbook/assets/image%20%284%29.png)

The killer features of the Equilibrium framework are its protection of EOSDT-to-USD parity and its ability to maintain a high level of confidence in the framework’s responsiveness to market events.

Reviewing the main two protections in place:

* the EOSDT generator needs to **post sufficient EOS collateral to fulfill at least 170% of the generated EOSDT value** \(measured at 1 EOSDT = $1 USD\).

Assuming no margin calls, liquidations or adverse market action, the 170% requirement theoretically protects EOSDT’s $1 parity from a 41% maximum drawdown event \(i.e. 70% less collateral\).

* The EOSDT generator is heavily incentivized to post more than the minimum 170% collateral, or else there is a material **risk of auto-liquidation with a 20% liquidation penalty**. If the EOSDT generators can’t post enough, they can alternatively repay the position’s original amount voluntarily. Either way, the system’s collateral-to-position ratio benefits from a natural resistance level. In other words, the average EOSDT generator should display strong risk-averse behaviors against collateral drops that would exceed the resistance level. 

To avoid the liquidation penalty, a participant would post in excess of 50% or 100% of the minimum required collateralization ratio.

In the following analysis, we consider a mono-currency EOS collateral framework and its parameters, since EOS is the initial collateral asset supported by Equilibrium at the time of writing.

## **Insight on Equilibrium participant behavior: the DAI-ETH case**

Equilibrium is a new project with no historical data yet. EOS price history is similarly only available since the January 2018 launch of EOSIO. In order to mitigate our lack of EOSDT and EOS data, we have modeled data on the comparable DAI stablecoin \(with ETH as collateral\) to gain insight on how EOSDT participants will behave. EOS warrants a conservative approach to our analysis because its returns have a much higher risk profile.

Let’s start with a few simple observations of the collateral to position ratio:

Like EOSDT, the DAI has also a penalty fee for auto-liquidation around 15%. The correlation between ETH returns and DAI Collateral to debt ratio movements unsurprisingly exceeds 65% for daily movements and 75% for weekly movements: the ETH collateral to debt ratio movements highly correlate with the ETH returns.

It furthermore looks like the 250% ETH collateral to debt ratio could be the _natural resistance level_ that we mentioned earlier. The [collateral](https://mkr.tools/system) to debt ratio hardly goes below this level.

The 250% resistance level surprisingly does not reduce the overall risk of the collateral to debt ratio, as measured by volatility and higher order moments. Above the resistance level, movements in the collateral to debt ratio display sharply increasing volatility and convexity measures, as shown in table \[1\] and figures \[1\] and \[2\].

|  | min | max | mean | median | stddev | skew | kurtosis |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| EOS Returns | -27.98% | 42.6% | 0.04% | -0.14% | 7.71% | 0.96 | 5.29 |
| ETH Returns | -18.68% | 18.07% | -0.38% | -0.23% | 5.48% | -0.10 | 1.44 |
| DAI-ETH Collateral to debt movements | -46.40% | 60.89% | 0.01% | -0.17% | 12.0% | 0.15 | 3.43 |

![Figure \[1\]: Densities of the ETH/USD log-returns and the ETH collateral to DAI debt ratio movements \(empirical\). Data source: CoinMarketCap and mkrtools](../../.gitbook/assets/image%20%282%29.png)

![Figures \[2\]: Densities of the ETH/USD log-returns and the ETH collateral to DAI debt ratio movements \(gaussian kernel fit\). Data source: CoinMarketCap and mkrtools](../../.gitbook/assets/image%20%281%29.png)

## **An analysis of the collateral asset: the EOSDT-EOS case**

The EOS daily log-returns essentially display most of the [characteristics](http://rama.cont.perso.math.cnrs.fr/pdf/empirical.pdf) seen in stocks and equity indexes. By this we mean,

* Absence of autocorrelations \(see figure \[3\]\). 
* Heavy tails \(kurtosis &gt; 3\): EOS distribution is leptokurtic with fat tails. 
* Volatility clustering \(see figure \[4\]\): Autocorrelation in the absolute returns process is generally a sign that high volatility returns tend to cluster: volatility is stochastic. If we have an extreme absolute log-return, there is a high probability that the absolute log-return on the following day will also be high. 
* Slow decay of autocorrelation: decay is typically a power law of time. Slow decay is a sign of long-range memory for the absolute or squared returns process: EOS volatility can be projected with better results if the model learns from the long history of volatility behavior \(see the rough volatility model and its calibration on historical intraday volatility by [Gatheral and Rosenbaum](https://arxiv.org/pdf/1410.3394.pdf), for example\). EOS’s limited history doesn’t let us conclude for significant correlations above order 10 \(correlation of absolute log-returns with 10-day lagged absolute log-returns\), as shown by the autocorrelogram, but more investigation in that direction is warranted. 

Accounting for stylized facts around logarithmic EOS returns, we propose a framework using an intuitive and conservative approach similar to [Jurek’s](https://www.nber.org/papers/w17422.pdf).

Our objective is to determine a measure of crash risk, and _in fine_, a reasonable liquid portion of the total collateral that is required to protect Equilibrium from a crash. Let’s assume here that the liquid portion can be sold instantly on the markets, without friction. Our concern is to make sure that there’s enough collateral available to be sold. The proceeds of the sale should at least cover the generated EOSDT value \(and potential penalties: 20% of the current position\).

The remaining portion can be optimally staked, frozen for three days, and then used to vote for block producers, vote on propositions for changes in parameters, and enjoy other powers that we described in the previous article [“_Equilibrium infrastructure: Roles and Voting Mechanisms._”](https://blog.eosdt.com/equilibrium-infrastructure-roles-and-voting-mechanisms/)

![Figure \[3\]: Autocorrelations of daily EOS/USD log-returns. Data source: CoinMarketCap](../../.gitbook/assets/image%20%2811%29.png)

\_\_

![Figure \[4\]: Autocorrelations of daily EOS/USD absolute log-returns. Data source: CoinMarketCap](../../.gitbook/assets/image%20%285%29.png)

## **The Threshold for Staked EOS Collateral: A Robust Framework**

**STEP 1: adjust the collateral asset returns for stochasticity.**

In order to eliminate the effects of stochastic volatility, the first step is to **scale each daily return by a measure of lagged volatility**. Applying the suggested measure by [Jurek](https://www.nber.org/papers/w17422.pdf), the chosen volatility measure is the three-month lagged standard deviation. The resulting log-return to volatility ratios is a series of z-scores.

No matter the market’s regime or volatility state, we are looking at the jump rather than the level. In other words, we are looking for negative shocks or surprises in EOS volatility and returns. These shocks are the only movements that can break the _natural resistance level_ without warning. EOSDT generators and bad debt keepers wouldn’t have any time to either replenish their collateral position or sell it in the markets at a reasonable price. Such are the extreme events that drive the Equilibrium insolvency risk.

**STEP 2: define the empirical crash distribution.**

In order to isolate the collateral’s crash risk exposure, we have to define a lower threshold below which z-scores are considered “crash” material. Given the limited historical data on EOS, we set the threshold at -2.0 for our exercise.

At a z-score of -2.0 and a standard deviation level of 5% \( corresponding to an annualized standard deviation at 200%\), the daily log-return equivalent is -10%.  
At a z-score of -6, the daily log-return equivalent is -30%.

If no portion of the collateral is staked, the 41% required haircut and an extra 20-30% posted by risk-averse EOSDT generators should largely compensate for extreme adverse market movements \(~ -60%\). In these conditions, staking a portion of the collateral can be a reasonable option.

Figure \[5\] outlines the selected crash observations \(highlighted in red\).

![Figure \[5\]: EOS/USD Z-scores computed with the daily log-return divided by 3-months lagged standard deviation. Data source: CoinMarketCap](../../.gitbook/assets/image%20%2810%29.png)

The conditional crash distribution is obtained by transforming the z-scores into losses. The current volatility level is used with the distribution of z-scores to simulate a series of potential losses:

![](https://blog.eosdt.com/content/images/2019/04/image-2.png)

**STEP 3: fit a Pareto distribution to the crash risk distribution.**

The Generalized Pareto Distribution \(GPD\) is a classic alternative to the normal distribution to better fit the probability distribution of extreme events. It has two parameters: the scale parameter and the shape parameter. The maximum likelihood method produces point estimates of these parameters, as well as a standard error that we use to determine a 99% confidence interval.

The worst z-score observed is -3.36, computed with a -15.75% negative return scaled by a lagged three-month standard deviation at 4.68%. It is not the worst return recorded — that distinction belongs to a -30% return recorded on Sep 4th, 2017 with a standard deviation at 10.40%. -3.36 is the worst negative **shock.**

The worst z-score \(worst shock\) combined with the worst volatility level observed sets a potential negative return at **-34.96%**!

Furthermore, the worst z-score **observed** is not the worst z-score of the population of log-returns. The observed z-scores constitute a sample of the population of log-returns. We mitigate the sample bias using the **99%** confidence interval.

The confidence interval on the GPD parameters implies a confidence interval on the quantile of the worst z-score observed. In this interval, the lowest quantile candidate for the -3.36 worst z-score is only at 0.655. In other words, **if we have a strong negative return that belongs to the crash distribution \(z-score &lt; -2\), then the conditional probability that its corresponding z-score exceeds the -3.36 level is 34.5% regardless of the current volatility level**. If the three-month lagged standard deviation is at 10% with a shock level at -3.36, then the equivalent negative return is -33.6%.

![Figure \[6\]: EOS/USD Crash distribution \(in red\) fit to a Pareto distribution. Data source: CoinMarketCap](../../.gitbook/assets/image%20%2813%29.png)

**STEP 4: Determine a safe staked portion of the collateral.**

The probability of a crash multiplied by the conditional level of the crash should not exceed the unstaked _liquid_ collateral. Any remaining portion could theoretically be staked.

We can fit a gamma distribution on the negative z-scores, or similarly a beta distribution on the crash distribution. Both distributions have very appealing properties: we can derive the cost of insuring a crash ξ from a simple closed-form formula. The cost is written as the product of the probability of observing market failure \(π Q\) multiplied by the exposure to the crash, which is the expected shortfall above the critical z-score level.

_**a**_ **and** _**b**_ are the parameters from our conservative beta distribution.

_**β**_ a parameter that measures the loss we want to insure against such that: \(1-x\)_β_ -1 is the consequent loss function where x is the loss \(crash\) on the underlying EOS.

**Ɣ** is a risk aversion parameter.

![](https://blog.eosdt.com/content/images/2019/04/image-8.png)

A simplified version \(_β_=1\) is given by:

![](https://blog.eosdt.com/content/images/2019/04/image-4.png)

To fit the beta distribution on the crash series, we enforce two constraints as in Jurek’s article \[3\]:

* the median of the crash distribution should be equal to the median of the beta distribution
* the quantile of the worst observed z-score should equal to the most conservative quantile derived from step three. 

The beta distribution estimation over crash returns gives the following estimates:

z-score threshold of -2.0: a=25.6, b=177.7 \(figure \[7\]\).

z-score threshold of -1.5: a=18.9, b=151.8.

z-score threshold of -1.0: a=8.34, b=91.4 \(figure \[8\]\).

Choosing Ɣ=2.5:

z-score threshold of -2.0: the resulting loss above threshold is at 13%.

z-score threshold of -1.5: the resulting loss above threshold is at 11%.

z-score threshold of -1.0: the resulting loss above threshold is at 9%.

Assuming a safe collateralization ratio of 250% and a three-month lagged standard deviation at 10%, the loss in this ratio would amount to a 47.5% drop \(10% of the collateral value, and a 9% excess loss in value\), which would leave us with a 202.5% collateralization ratio if it happened. This is still above the 170% critical level.

Note that over three days, assuming the same level in market crash every day, this exposure would be 1-\(1-47.5%\)^3 = 85.53%. Starting from a safe collateralization ratio of 250%, the loss in this ratio would amount to 213%, which would leave us with a 37% collateralization ratio if it happened. This is below the 100% parity level. In this case, a cut of 63% of the 250% EOS collateral ratio must remain liquid and reserved for potential liquidation. Furthermore, at least 133% should be in reserves if we wish to satisfy the 170% critical level. We can then consider staking the remaining collateral for three days.

The model offers a robust and conservative methodology to determine whether a portion of the collateral can be staked. But our analysis is far from complete. Questions remain over the choice of the standard deviation measure, the risk aversion parameter, the probability of having extreme crash returns over three consecutive days, and most importantly, the choice of the z-score threshold that should ideally be below -6. The small sample of daily data we are working with clearly has its limitations.

![Figure \[7\]: density of the 19 worst crash returns corresponding to a z-score &amp;lt; -2.0 - Constraint \(at 50% and the lowest percentile of the max crash return obtained by fitting the Pareto distribution\): Beta fit in blue.](../../.gitbook/assets/image%20%286%29.png)

![Figure \[8\]: density of the 64 worst crash returns corresponding to a z-score &amp;lt; -1.0 - Constraint \(at 50% and the lowest percentile of the max crash return obtained by fitting the Pareto distribution\): Beta fit in blue.](../../.gitbook/assets/image%20%288%29.png)

Given the numerous sources of liquidity for trading EOS today, the expected collateral to debt ratios \(~250%-300%\) would sit in a very high range compared to other traditional liquid-like collateral assets.

There are obviously always more factors to be considered, like market liquidity risk and market impact. When all these measurable factors are accounted for, only the black swan factor remains. A black swan is, by definition, impossible to model — it’s unpredictable.

EOSIO and Equilibrium can give their community the power to deal with black swans the same way they are dealt with in the real world: by adapting or changing the rules. More than a set of static rules and parameters, the EOSDT Equilibrium is a robust, dynamic framework where a variety of models and parameters are possible as long as it’s justified and convincing to larger group of voters.

_With this article and many to come, we are taking serious steps to educate the Equilibrium community, to instill confidence in the framework’s mechanisms, and to ultimately benefit the community itself._

\_\_

\[1\] CONT, EMPIRICAL PROPERTIES OF ASSET RETURNS, STYLIZED FACTS AND STATISTICAL ISSUES. October 2000.

\[2\] GATHERAL, ROSENBAUM, VOLATILITY IS ROUGH, October 2014. [https://arxiv.org/pdf/1410.3394.pdf](https://arxiv.org/pdf/1410.3394.pdf)

\[3\] JUREK, CRASHES AND COLLATERALIZED LENDING, NATIONAL BUREAU OF ECONOMIC RESEARCH, Sep 2011, [https://www.nber.org/papers/w17422.pdf](https://www.nber.org/papers/w17422.pdf)

This article contains statements and information on a variety of topics, you should be aware that such statements involve unknown risks that may cause actual results to be materially different from any future performance suggested herein. Neither the Equilibrium Framework nor any of its affiliates or Members make any representations or warranties that any such statements in this Paper are accurate and they should not be relied upon.

This article should not constitute an offer to buy or sell, or a solicitation of an offer to buy or sell any assets contemplated herein. This article does not purport to identify or define all of the risks which may be associated with the Equilibrium Framework or its associated Services.

