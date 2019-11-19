# Fees and flows

Since the launch of Equilibrium’s EOSDT stablecoin project earlier this year, the framework has naturally grown around it. The fees and fee logic have grown as well, and have become too cumbersome to understand. This article will structure all the fees and fund flows that currently exist inside the Equilibrium framework.   
The main takeaway is that all fees within the Equilibrium framework are one way or another related to the NUT utility token, because this is where NUT derives its value.   
  
Apart from fees, there are other flows of funds in the system related to EOS’s DPoS architecture. We will break down all these flows here with numerical examples.

{% tabs %}
{% tab title="Equilibrium fee" %}
**Description**

Position holders may be incentivized to sell or expand their EOSDT holdings with the Equilibrium fee. The framework applies the Equilibrium fee every time a user takes an action and calculates this fee based on an annual percentage rate, defined by Equilibrium’s governance smart contract. At the launch of the framework, the Equilibrium fee will be set to 0% APR.

If there’s an excess market supply of EOSDT, governance may decide to raise the applicable fee to induce position holders to sell off their “expensive” positions, lowering the supply of stablecoins. If there’s excess demand for EOSDT, governance may lower the interest rate to induce users to enter more “cheap” positions, increasing the total supply. This is reminiscent of how central banks adjust short-term interest rates to control the economy’s supply of money.

**Where to look?**

[**eosdtcntract.ctrsettings.stability\_fee**](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100)
{% endtab %}

{% tab title="Admin fee" %}
**Description**

This fee is set to 1% per year by default, payable in NUT tokens. This fee is due when users wind down their EOSDT positions, and it’s proportional to the amount of EOSDT being repaid.  
  
Equilibrium partners may collect 50% of this fee through a referral program. The actual percentage depends on the [referral\_ratio](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100) parameter.

**Where to look?**

[**eosdtcntract.ctrsettings.governance\_fee**](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100)
{% endtab %}
{% endtabs %}

There are three main mechanisms so far that benefit both collateral holders and NUT token holders inside Equilibrium. Let’s briefly touch on the mechanics of each one of these revenue streams, and then consider numerical examples which would further clarify things. 

### **Penalty from liquidating undercollateralized positions.**

When any position becomes undercollateralized \(the value of its collateral falls below 130% of its generated EOSDT value\) it is subject to liquidation and will be stripped of a [liquidation\_penalty](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100) fee.  
  
It's worth considering a numerical example now. Let’s assume I have a position with 100 EOSDT generated, the EOS price is $5 USD, and I have pledged 26 EOS in total.  


The Collateralization Ratio for my position will be: CR = EOS collateral \* EOS price / EOSDT generated = 5 \* 26 / 100 = 1.3 = 130% — so it is subject to liquidation.  
  
When liquidated, I will pay: EOSDT generated \* \(1 + liquidation\_penalty\) / EOS price = 23 EOS. I will be left with my 100 EOSDT and 3 extra EOS sitting on my empty position. In the meantime, the liquidator contract will have the following state:  
  
bad\_debt = 100 EOSDT  
eos\_balance = 23 EOS  
nut\_collat\_balance = 0 EOS  
  
Now any willing user can come to the liquidator contract, bring EOSDT to “burn” bad\_debt, and receive EOS from eos\_balance in return. This is called a collateral auction. To incentivize people to participate in this auction, there is a [eosdtcntract.ctrsettings.liquidator\_dicount](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100) of 3% off current [prices on the rates contract](https://bloks.io/account/eosdtorclize?loadContract=true&tab=Tables&account=eosdtorclize&scope=eosdtorclize&limit=100&table=orarates).  
  
Let’s assume I’m one of these willing users: I bring in 100 EOSDT and get back:  
EOSDT\_in / \(EOS price \* \(1 - liquidator\_discount\) = 100 / \(5 \* \(1 - 0.03\)\) = 20.6186 EOS. After I buy out all the bad\_debt from the liquidator contract, it will have the following state:  
  
bad\_debt = 0 EOSDT  
eos\_balance = 2.3814 EOS  
nut\_collat\_balance = 0 EOS  


Now the system has no “bad\_debt”, so eos\_balance can be distributed among collateral holders and NUT token holders. The proportion is governed by the [nut\_auct\_ratio](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100&table=ctrsettings) parameter.  
  
2.3814 \* \(1 - nut\_collat\_balance\) = 2.0956 EOS will be transferred to the positions contract and distributed among collateral holders, and the liquidator contract will have the following state:  
  
bad\_debt = 0 EOSDT  
eos\_balance = 0.2858 EOS  
nut\_collat\_balance = 0.2858 EOS  
  
Notice that system updated nut\_collat\_balance. This happened because bad\_debt went to zero, the system returned part of the profit back to its position holders, and is now ready to distribute profits to NUT holders. Now any willing user may come to liquidator contract and bring in NUT tokens to buy out this excess EOS. To incentivize people to participate in this auction, there is a [eosdtcntract.ctrsettings.nut\_dicount](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100) of 6% off current [prices on the rates contract](https://bloks.io/account/eosdtorclize?loadContract=true&tab=Tables&account=eosdtorclize&scope=eosdtorclize&limit=100&table=orarates) for this excess EOS. 

  
Let’s assume I’m one of these willing users. I should bring nut\_collat\_balance \* EOS/NUT price \* \(1 - nut\_discount\) = 0.2858 \* 0.2 \* \(1 - 0.06\) = 0.0537 NUT \(we’re assuming EOS/NUT price is 0.2\) to buy all of this EOS.  
  
I have obtained 0.2858 EOS and the liquidator contract has 0.0537 NUT. 10% of this NUT will be burned, and the remaining 90% will be transferred to the [governance contract](https://bloks.io/account/eosdtgovernc). The 10% and 90% weights are liquidator contract settings:  [eosdtliqdatr.liqsettings.burn\_rate](https://bloks.io/account/eosdtliqdatr?loadContract=true&tab=Tables&account=eosdtliqdatr&scope=eosdtliqdatr&limit=100&table=liqsettings) and [eosdtliqdatr.liqsettings.gov\_return\_rate  
  
](https://bloks.io/account/eosdtliqdatr?loadContract=true&tab=Tables&account=eosdtliqdatr&scope=eosdtliqdatr&limit=100&table=liqsettings)One additional thing is worth mentioning here: if liquidation took place and there are no users willing to participate in a collateral auction on the liquidator contract, then it may happen that EOS price continues to fall. At some point there won’t be enough EOS to cover all the “bad debt” on the liquidator contract. In this case the system will be punishing all the collateral holders by taking a fraction of the total collateral to cover the shortage. If we return to our earlier example with 23 EOS and 100 EOSDT bad\_debt sitting on the liquidator contract, this will happen when EOS price falls from 5 USD to 100/23 = 4.3478 USD and below. Astute readers may conclude that the break-even drop in EOS price depends on **liquidation\_penalty** and **liquidator\_discount** parameters.   
  
Here’s the table that calculates the break-even percentage drops for EOS price.  
Current parameter settings are marked in green. “Prohibitive” settings are marked in red.  
The system allows for ~10% drop before we start tapping into everyone’s collateral. Given EOS’s price volatility of around 8% a day, a drop of this magnitude is expected to occur in at least 4 hours in more than 99% of cases. Given that the EOS rate is updated every minute by an oracle contract and we’re expecting to increase this to every 15 seconds, we’ll eventually have a high margin of safety here.

![](../.gitbook/assets/image%20%289%29.png)



### **REX profit**

The EOSDT contract stakes all of the user collateral collectively to REX and earns profit in the form of REX price appreciation. Currently, eosdtcntract is the [biggest REX lender](https://eosauthority.com/rex/statistics?network=eos).

Profit from REX is calculated and taken out every [eosdtcntract.ctrsettings.stake\_period ](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100)

A fraction of this profit, controlled by the [profit\_factor](https://bloks.io/account/eosdtcntract?loadContract=true&tab=Tables&account=eosdtcntract&scope=eosdtcntract&limit=100&table=ctrsettings) parameter, goes to the liquidator contract and updates the nut\_collat\_balance \(given that there is no bad\_debt there\). The rest is returned straight to the positions contract and collateral holders.        
  
For example, if REX earned 300 EOS profit over the past 5 days, then:

Collateral holders will collectively receive: 300 \* \(1 - profit\_factor\) = 264 EOS   

Liquidator will have the following state given the absence of bad debt:  
  
bad\_debt = 0 EOSDT  
eos\_balance = 36 EOS  
nut\_collat\_balance = 36 EOS   
  
Next in the liquidation case \(just as above\), users buy out excess EOS from the liquidator contract for NUT. This NUT is then partially burned and partially transferred to the governance contract for the benefit of all NUT holders.    
****

### **BP voting rewards**

Equilibrium offers its users a nice revenue stream in the form of rewards that block producers agree to pay for in exchange for receiving the collective vote of the entire system collateral. The entire logic works the following way:  


1. Block producers register with our smart contract by calling the [bpregister](https://bloks.io/account/eosdtgovernc?loadContract=true&tab=Actions&account=eosdtgovernc&scope=eosdtgovernc&limit=100&action=bpregister) action on the governance contract, directly from the block producer’s account. After setup, the block producer should appear in the [govbpparams](https://bloks.io/account/eosdtgovernc?loadContract=true&tab=Tables&account=eosdtgovernc&scope=eosdtgovernc&limit=100&action=bpregister&table=govbpparams) table. To enable an account, a BP must then transfer EOS to the eosdtgovernc contract with memo `{"bp_deposit":"bp_account_name"}`. This transfer in turn is accepted from any account. 
2. When registering, BPs specify the amount of daily rewards they’re willing to pay \([reward\_amount](https://bloks.io/account/eosdtgovernc?loadContract=true&tab=Actions&account=eosdtgovernc&scope=eosdtgovernc&limit=100&table=voters&action=bpregister) parameter\). The amount should be greater than the [min\_reward](https://bloks.io/account/eosdtgovernc?loadContract=true&tab=Tables&account=eosdtgovernc&scope=eosdtgovernc&limit=100&table=govsettings&action=bpregister) setting, which is 5.0000 EOS by default.
3. If a block producer isn’t registered with the Equilibrium governance contract, it won’t be considered a candidate for **eosdtbpproxy** proxy voting.
4. NUT holders vote for registered BPs. Since each BP agrees to pay a different daily reward in EOS, NUT holders have an incentive to vote for those BPs who offer greater daily rewards.
5. Actual proxy voting happens in real time: when a NUT holder casts a vote for a BP, the proxy revotes for the top 20 BPs by NUT count. There is also a separate CRON service to ensure the proxy votes for BPs at some regular interval.
6. If it happens that by the time the proxy needs to vote for BPs and it happens that there are no active reward-paying BPs on the contract, the proxy will vote for the top BP by NUT count. 

  
Rewards are calculated and collected each time the proxy revotes for a list of BPs. Rewards are payable in EOS and distributed among collateral holders and NUT holders according to the same logic as the above REX and liquidation rewards. This distribution is governed by the [reward\_weight](https://bloks.io/account/eosdtgovernc?loadContract=true&tab=Tables&account=eosdtgovernc&scope=eosdtgovernc&limit=100&table=govsettings) parameter.  
****





