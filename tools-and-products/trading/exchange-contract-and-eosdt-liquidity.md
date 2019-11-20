# Exchange contract and EOSDT liquidity

An integral part of the Equilibrium stablecoin system is the incentivization of external agents called guardians. We’ve already covered on-chain guardian contracts in detail here. These external actors require liquidity in order to quickly obtain collateral from the liquidator smart contract and sell it at a profit. So we’ve developed an on-chain liquidity contract to offer them this opportunity.

The equiexchange contract is really simple under the hood. It currently supports two trading pairs: EOS/EOSDT and NUT/EOS. These trading pairs are always quoted in reference to the current price from the rates contract.

Interactions with the exchange contract are very simple: there are two transfers, buy and sell. Both require a pair\_id and quantity. That's it! Here are the buy and sell examples:

`Buy: $ ./cleos transfer testtesttest equiexchange "3.000000000 EOSDT" '{"pair_id":1}'`  
`Sell: $ ./cleos transfer testtesttest equiexchange "1.0000 EOS" '{"pair_id":1}'`

Guardians can integrate this functionality into their code to buy out liquidated collateral and sell it to the exchange in a single transaction.

We aim to eventually make the equiexchange contract into a fully-functional on-chain decentralized exchange. It won’t be the kind of DEX you’ve seen in EOS so far, but will be one with a simple and understandable API to support lending, borrowing, margining, and smart order-types.

## **On-chain EOSDT liquidity**

EOSDT is currently available for trade on the following EOS-based services:

* [**Newdex**](https://newdex.io/trade/eosio.token-eos-eosdt)
* [**Findex**](https://findex.pro/trade/eosdtsttoken_EOSDT-eosio.token_EOS)
* [**YOLO**](https://yoloswap.com/swap/eos-eosdt)
* [**Bancor**](https://www.bancor.network/token/EOSDT)

Users can trade EOSDT against EOS on any of the DEXes or connectors specified above. There are interesting arbitrage opportunities here, especially if one considers arbitraging Bancor or Yolo against Newdex, or against our equiexchange contract. Both Bancor and Yolo provide a form of algorithmic liquidity. Bancor follows a concave utility-like function, and the price inside its connector depends on actual connector balances. YOLO offers an exponential liquidity based on the amount of EOS supplied to it.

Both connectors have their operational specifics, but arbitraging them is pretty simple: as soon as you see that EOS/EOSDT price deviates either in Bancor or in Yolo enough to cover your trading fees and spreads on Newdex, you make a trade.

