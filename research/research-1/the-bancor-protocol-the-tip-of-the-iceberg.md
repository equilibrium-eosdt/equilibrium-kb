# The Bancor Protocol: the Tip of the Iceberg

![](../../.gitbook/assets/image%20%283%29.png)

Equilibrium recently announced the launch of its new R&D division, [Equilibrium Labs](https://equilab.io/), aiming to develop new price-pegging algorithms and structures that will bring enhanced liquidity to the decentralized finance market.  


It was a logical choice to start our research with the [Bancor](https://about.bancor.network/) protocol and [Daniel Larimer’s pegging algorithm](https://medium.com/@bytemaster/high-liquidity-price-pegged-token-algorithm-d86d71188162).  


The Bancor protocol is a simple and elegant solution that lets tokens be converted to other types of tokens. The price of conversion is solely determined by the ratio of the transparent reserves of each token. It’s an ambitious initiative named after Keynes famous [proposal](http://imsreform.imf.org/reserve/pdf/keynesplan.pdf) to create a “_c_urrency union” that would “generalize the essential principle of banking as it is exhibited within any closed system. This system is the necessary equality of credits and debits, of assets and liabilities.”  


Extensive research has already formalized and simulated Keynes’s model. Looking at [Valdecantos and Zezza](http://models.sfc-models.net/vz2015/models.pdf)’s work, we can immediately weigh the potential complexity of modelling a real-world economy with four “countries:” the United States, Europe, China, and the rest of the world.  


In the real-world economy, a country’s inhabitants work, produce, export, import, and consume. Agents have risks and preferences. Naturally, prices will reflect these risks and preferences. By contrast, the price of[ a Bancor Relay](https://blog.bancor.network/how-bancor-relays-work-c712a374374f) ****is completely determined by the amount of available tokens held in reserve \(eg. the Bancor “connectors”\). The concept of risk is missing, and with good reason.  


The Bancor protocol is a fundamental concept that should be considered the backbone of a central bank. And like a central bank, the Bancor protocol structure is not profit-oriented.  


From a stablecoin perspective, Bancor can be used to synthesize the most trivial model of a central bank with a pegged currency policy.  


### Daniel Larimer’s pegging algorithm 

For instance, Larimer describes a pegging algorithm based on the [Bancor](https://storage.googleapis.com/website-bancor/2018/04/01ba8253-bancor_protocol_whitepaper_en.pdf) protocol in a recent [article](https://medium.com/@bytemaster/high-liquidity-price-pegged-token-algorithm-d86d71188162). The new Market Making token \(MMS\) introduced there is a wrapper around a Bancor Relay token with two connectors — one for the collateral asset \(EOS, for example\) and the second one for the USD-pegged asset. Pegging the Bancor price to the market price consists of rebalancing the connectors’ quantities to keep their market value equal, or within a tight range of ± 2%. The market value of each connector is based on a trusted price feed or a 24-hour median. Rebalancing the connectors’ quantities happens by moving collateral in and out of an excess collateral reserve.  


The perfectly named “pegging algorithm” could be perceived as the first simple representation of a country choosing to peg its currency to that of another. The country would use its currency reserves to intervene in the market if it became necessary to enforce the peg. Similarly, the “pegging algorithm” can use the excess collateral to buy a depreciated USD-pegged token.  


However the “pegging algorithm” is not an MM algorithm. Larimer points out that those willing to hold MMS tokens are those willing to be slightly leverage-long in a collateral asset \(such as EOS\). __They are the ones willing to hold inventory risk. They essentially accept this risk for free or little benefit.  


So how would the structure compensate for inventory risk?  


Let us resume the rational market maker’s \(MM\) objectives:  


* A rational MM would seek to maximize profits under inventory risk constraints. The market maker should be rewarded for holding this risk. 
* A market maker will also be concerned with running out of inventory, in which case they wouldn’t be able to quote both a bid and ask. They prefer to hold a mix. 

Once again, the Bancor Protocol is a decentralized exchange facility where anyone can show a price that’s determined and fully backed ****by transparent quantities. For a participant, the price ratio has to reflect a ratio of tangible reserves, but the ratio of tangible reserves could reflect the participant’s risks and preferences. The ratio of tangible reserves can be the output of an algorithm ****\(like the “pegging algorithm”\) that accounts for inventory risk.  


In other words, the price produced by Bancor is only the tip of the iceberg. It could be the price that an MM decides to publish. We know it’s not a fake price because it’s backed by actual quantities. What we don’t know is how the quantities will be adjusted: we don’t know the MM actual risks \(like the MM’s true inventory\) or preferences \(like the MM’s aversion to risk\). In these conditions, anyone can try to reverse engineer the MM’s risks and preferences. Anyone can try to build a better model to arbitrage the MM: “the \(stablecoin\) shorts” would start to compete.  


Competition between rational MMs might be what is needed to effectively attract liquidity. Alternatively, can a community reach a consensus on a more advanced MM model. Can a community vote to set the parameters of this advanced MM model on a regular basis?  


### The Bancor Framework... With Custom Preferences?  <a id="the-bancor-framework-with-custom-preferences"></a>

Let’s illustrate the above with a simple model that is widely used in economics theory.  


Given the sole dependency of the Bancor relay price on the connectors’ quantities, the model reminds me of one that is at the heart of some of the most advanced economic models: the [Dixit-Stiglitz model](https://www.brown.edu/Departments/Economics/Faculty/Matthew_Turner/ec2410/readings/Dixit_Stiglitz_AER_1977.pdf).  


The Dixit-Stiglitz model follows this simple hypothesis: “a consumer who is indifferent between the quantities \(1,0\) and \(0,1\) of two commodities prefers to hold a mix.” In other words, agents love variety.  


In his [40-year review](https://www8.gsb.columbia.edu/faculty/jstiglitz/sites/jstiglitz/files/Monopolistic%20Competition%2C%20he%20Dixit-Stiglitz%20Model....pdf) since the Dixit-Stiglitz model, Stiglitz states: _****_“If the returns to shares of firms” \(aka tokens\) “are not perfectly correlated, then each is an imperfect substitute for the other. Individuals will want to hold all shares in their portfolio — just as in the Dixit–Stiglitz model, individuals want to buy all commodities” \(aka tokens\) ”that are produced.”  


![](https://blog.eosdt.com/content/images/2019/07/1graph.png)

Bancor is a limit case of the Dixit-Stiglitz utility. Because inventories can never deplete, there will always be a mix. For two relays with similar connector positions, Bancor will always show the same consensus price, regardless of preferences. If the connector quantity of an asset drops, the Bancor price for the asset will soar. But what if the MM wants to take more risk and sell more of the asset, despite the low inventory? Dixit-Stiglitz lets the experienced market-makers show a more aggressive offer, should they believe that the price will revert to its mean. It gives market-makers more freedom.  


The table below shows how the alternative Dixit-Stiglitz price would evolve compared to the Bancor price.  


![](https://blog.eosdt.com/content/images/2019/07/4graph.png)

![](https://blog.eosdt.com/content/images/2019/07/2graph.png)

### A Generalized Bancor Protocol Wrapper  <a id="a-generalized-bancor-protocol-wrapper"></a>

Dan Larimer’s algorithm lets the Bancor Relay show a price that is within a spread, close to a feed price: the 24-hour median price. In fact, the feed price can be anything. If we rebalance the Bancor connectors’ quantities such that their ratio equates the feed price \(FP\): qEOSDTBancorqBancorEOSDT / qEOSBancorqBancorEOS = 1 / FP, then the Bancor Relay formula will force the Bancor price qBancorEOS/EOSDTqEOS/EOSDTBancor to revert to FP.

For an agent with specific preferences, _FP_ should be the price that maximizes the Dixit-Stiglitz Utility.

For a Market Maker, _FP_ should be within market bid-ask spread, closer to the bid if there is a buying interest, and closer to the ask in case there is a selling interest. The choice of feed price is the critical leeway we need to make the MMS algorithm a true market making algo.

FP could factor in a market maker’s preferences and risk aversion. We can let the market maker algorithm modify the connector quantities to target any set price.  


![](https://lh6.googleusercontent.com/nFdo8YywipAqQZGPNK7OWPL1bzbJ4eJDb68t2nPp29CswxQmnGWaI-NoeGsk117IaZPvlVlkD9p0Zdo1ql-5gVRIS_UPgMvDISu0kRiY_Ps3x47e8RTxxfxiHwou9onzgcLxqT_-)

### A Simple Wrapper: The Dixit-Stiglitz Protocol  <a id="a-simple-wrapper-the-dixit-stiglitz-protocol"></a>

For an EOS-EOSDT Relay, an agent with preferences holds a total quantity QEOSQEOS of EOS tokens and a total quantity QEOSDTQEOSDT of EOSDT tokens.

If they prefer to quote a more aggressive ask if the price goes up, or a more aggressive bid if the price goes down. The agent can set:  


$$ FP = (Q_{EOS}/Q_{EOSDT})^{1−ρ}$$

Then $$ q^{Bancor}_{EOS/EOSDT}$$ will be reset towards the optimal level that maximizes the agent’s Dixit-Stiglitz utility.

### An Advanced Wrapper: The Market-Maker Protocol  <a id="an-advanced-wrapper-the-market-maker-protocol"></a>

Unlike a nonprofit central bank, the MMs objective is to maximize the book value of their MMS tokens. The astute MMs can design an algorithm that target the equilibrium price that maximizes the expected book value under their inventory risk constraints over a specific investment horizon.  


This is the next step to build serious incentive for anyone to buy \(or create\) market-making tokens, and trust that their invested value will be maximized.  


In the context of Equilibrium Labs, there is room for research into an extended role for the NUT token holders. NUT holders could interact with or even manage such an EOS/EOSDT wrapper for EOS collateral liquidations. The transfer of more EOS to a Bancor Relay would intuitively drive the EOS/EOSDT price down to account for the keepers rebate.  


Stay tuned!  


\_\_

\[1\] [STIGLITZ. MONOPOLISTIC COMPETITION AND OPTIMUM PRODUCT DIVERSITY. American Economic Association. The american Economic Review, 1977](https://www.brown.edu/Departments/Economics/Faculty/Matthew_Turner/ec2410/readings/Dixit_Stiglitz_AER_1977.pdf)

