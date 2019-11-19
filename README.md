# Equilibrium overview

## Introduction

The Equilibrium framework is an intuitive technical environment for creating decentralized stablecoins and developing DeFi projects. Equilibrium-based products let the community generate assets pegged to multiple currencies against liquid crypto collateral, and use thoughtfully designed DeFi applications.

The Equilibrium framework has built a stack of core libraries to achieve this, running on the EOS blockchain to develop end user applications like gateway and block producer voting dApps that communicate with the core logic.

Equilibrium aims to provide a simple and intuitive experience for users who may or may not have relevant developer experience, and even may not be familiar with cryptocurrencies in general. The project takes a full-stack approach to achieve this, where each layer of the stack is designed with the end user in mind. The Equilibrium framework stack may be broken down according to the following logical layers:

* **Blockchain:**

  The best decentralized applications not only require fast transaction processing times, but an infrastructure robust enough to offer a high-quality experience to lots of users at once. Equilibrium is built on top of the EOS blockchain and EOSIO technology stack because it offers decentralized storage and general purpose infrastructure for running dApps more effectively than competitors. EOS proof-of-stake consensus is more natural and more promising, especially for multichain projects like Equilibrium because proof-of-work would require prohibitive amounts of computational resources if there are many blockchains. Others advantages of the EOS blockchain include its comprehensive documentation, fastest development times, and out-of-the-box on-chain governance. In contrast, most currently operational blockchain projects like Bitcoin and the original Ethereum use proof-of-work.

* **Equilibrium Core Contracts:**

  A set of smart contracts running on the EOS blockchain that contain the base logic of the framework, including: oracle service and external data subscriptions, EOSIO native stable currencies and their risk management, as well as decentralized on-chain governance with the help of blockchain PoS architecture and the framework’s utility token NUT.

* **Applications:**

  The EOSDT stablecoin and its gateway app are the first elements in an ecosystem of applications. They let end users to generate safe-haven currency that’s pegged to USD and backed by EOS collateral at all times. Applications take the form of external desktop/mobile or backend/blockchain software — they interact with Equilibrium to issue transactions and invoke code that forms the Equilibrium Core Contracts’ API. Third parties can also deploy custom smart contracts that their own applications can invoke, which in turn can leverage Equilibrium Core Contracts.

## Core functionality

Equilibrium is a fully on-chain liquidity management framework. The core of the Equilibrium framework consists of a bundle of smart contracts that govern how users interact with Equilibrium:

* **Positions contract**

  The position contract stores a user’s liquid digital assets in a unified pool, allowing for the management of this pool with help from on-chain governance. Equilibrium enables a family of EOSIO native assets whose values are managed using a stability mechanism. These assets are backed by a reserve of liquid digital assets supplied by users. The first stablecoin is EOSDT, pegged to USD. The positions contract also sets the logic for a user’s position management. It stores the framework’s global risk parameters, which specify how the system behaves, and governs the position creation process and a user’s position interactions.

* **Governance contract**

  Equilibrium uses an on-chain governance mechanism to manage and upgrade aspects of the framework that reside in the Equilibrium Core Contracts.

  Equilibrium’s global risk parameters are configured by a governance mechanism, which means that NUT holders get to vote on proposals to change the framework’s parameters. If the majority agrees on new parameters, those become the norm.

  Another important aspect of the Governance contract is the ability of NUT token holders to direct the liquidity pool to additional revenue streams available inside the EOS ecosystem, including \(but not limited to\): EOS REX resource lending, EOS block producer voting, and profit sharing.

  Last but not least: the Governance contract allows NUT token holders to create proposals and vote on them for effective decentralized autonomous management of the entire framework.

* **Rates contract**

  The Equilibrium framework provides access to real-time market prices thanks to its rates smart contract. Any third party account or smart contract can subscribe to the price feed and receive timely updates required for business logic. Outside of accurately knowing collateral prices for risk management, the Equilibrium framework needs relevant prices for the NUT utility token to calculate the corresponding fees within the framework.

  The Equilibrium framework works with Oraclize \(now called Provable\), Delphi Oracle, and Liquid Apps oracles to obtain timely market data on-chain. The system uses three different oracles for redundancy and calculates the median rate based on the data provided by these oracles. The median value is a final reference price used in all of the on-chain calculations.

* **Liquidator contract**

  Normal framework operations are only possible when each position’s collateral-to-debt ratio is above the liquidation ratio \(critical\_ltv parameter on positions contract currently set at 130%\). When checking position safety, the external reference price is used in order to establish the USD value of the collateral, while the USD par price is used to price the EOSDT liability. Thus, position safety is assessed in terms of USD at a target price 1 EOSDT = 1 USD. By liquidating positions that fall below the liquidation ratio, the system can ensure that the USD value of collateral backing circulating EOSDT remains within determined parameters. Liquidated positions accrue balances on the Liquidator contract.

  Collateral balance accumulates on the liquidator contract from positions that get margin called. This collateral is sold in exchange for EOSDT — a so-called “collateral auction.” Collateral auctions cover EOSDT liabilities originating from liquidated positions. The Liquidator contract sells EOS collateral at a discount from the reference price to incentivize EOSDT liability liquidation. It's a fixed price sell-off rather than an auction.

  All interactions with the Liquidator contract are done via transfers from any EOS account:

  * Transfer EOSDT when there is non-zero "bad debt" on the liquidator contract and receive EOS collateral at a 3% discount.
  * Transfer NUT when nut\_collat\_balance is non-zero to buy out excess EOS collateral at a 6% discount.
  * Transfer EOS when surplus\_debt is non-zero \(non-zero equilibrium fee, which is not the case right now\) on the liquidator     contract to buy out EOSDT at a 3% discount.

