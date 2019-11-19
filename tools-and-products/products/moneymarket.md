# EOSDT lending and borrowing pool

Basic design and development of financial applications calls for there to be mechanisms like money markets and the ability to lend or borrow stablecoins at some “risk-free” interest rate. The efficiency and liquidity of these decentralized money markets may then be used to build various derivative instruments on top of existing infrastructure. For example, contracts like futures and forwards derive their prices from the “no arbitrage” argument, so their on-chain existence and viability directly depends on the assumption that market participants can borrow money at the same risk-free interest rate as they can lend money.

The Equilibrium framework has a separate protocol in place that establishes money markets on the EOS blockchain. These money markets are pools of assets with algorithmically derived interest rates based on the supply and demand of an asset. Asset lenders and borrowers interact directly with the protocol, earning \(and paying\) a floating interest rate without having to negotiate terms like maturity, interest rate, or collateral with a peer or other third party.

Equilibrium currently operates an EOSDT pool where users may lend or borrow EOSDT stablecoins. The smart contract is available for public use, and any user or organization may use it to provide on-chain liquidity in their token of choice.

## **Key points**

1. The eosdtlbpools smart contract is an EOSDT pool where lenders and borrowers can interact with each other. Lenders post EOSDT, borrowers post collateral and are able to borrow EOSDT. 
2. Interest rates are calculated using continuous compounding and reinit\(\) logic, similar to the main positions contract: accrued interest rates are recalculated whenever there’s some interaction with the smartcontract. 
3. Interest rates are determined by the supply and demand of the underlying asset. When demand to borrow an asset grows \(or when supply is removed\) interest rates increase to incentivize additional liquidity.
4. Interest rates are floating, so every time the supply/demand changes, each loan will be subject to a new interest rate.
5. Borrowers pay interest on their loan from the collateral securing the loan.
6. Borrowers may supply several collateral tokens to borrow EOSDT, each with its specific LTV ratio. Initially Equilibrium supports following tokens for collateral EOS, CHEX, DAPP, PEOS, NUT. New collateral tokens may be added via governance.
7. Lenders earn an implicit interest rate based on actual borrowing utilization.
8. Borrowing interest rate model: `r = r0 + total borrowed / (total borrowed + total supply) * rX`
   * `r0` - base interest rate when there are no borrowers. r0 equals to Equilibrium fee + Admin fee. 
   * `Total borrowed` - total amount of EOSDT that has been borrowed from the fund so far.
   * `Total supply` - total amount of EOSDT available for borrowing \(this number doesn’t change when users borrow, and only changes when lenders deposit or withdraw money\).
   * `rX` - slope, which defines the terminal interest rate when all of the supply has been borrowed.
   * `r0`, `rX` are available to be set and changed via Equilibrium governance.

## **Primary use-cases**

### Lending

Individuals with long-term investments in EOS and tokens can use the Equilibrium money market as a source of additional returns on their investment. For example, a user who owns EOS can give their tokens to the eosdtlbpools contract and earn interest \(in EOS\) without having to manage the asset, fulfill loan requests, or take speculative risks.

### Borrowing

Traders looking to short a token can borrow it, send it to an exchange, and sell the token. This series of action sees traders profit from declines in overvalued tokens. DApps may borrow tokens for use in the EOS ecosystem, like using the governance functions of Equilibrium’s NUT token, or subscribing to Liquid Apps DSP with DAPP tokens.

