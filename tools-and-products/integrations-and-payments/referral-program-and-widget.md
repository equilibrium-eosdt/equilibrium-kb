# Referral program and widget

This is an additional piece of logic on eosdtcntract that allows for integrating EOSDT generation into your own web application or dApp — 50% of all NUT-based fees will be paid by stablecoin users.

Users must pay an admin fee in Equilibrium’s NUT in order to close an EOSDT position, and the referral program simply splits this fee in half — 50% of NUT tokens get burned, 50% goes to the referral partner.  


Those who want to participate need to register on EOSDT’s smart contracts in order to make sure they can get paid. This only requires a separate NUT transfer to our smart contracts, granting partners a referral ID.

  
****There are new settings related to the referral program:  
  
  
****

| **parameter** | **type** | **description** |
| :--- | :--- | :--- |
| referral\_min\_stake | asset **** | The minimum NUT stake required to register as a referral. This is 2 NUT by default.  |
| referral\_ratio | float | The ratio of admin fees that go to referrals as a reward. By default this is 50%. |

  
****New table **ctrreferrals**:

This table lists all the referrals with their respective staked amounts of NUT.  
****

| **parameter** | **type** | **description** |
| :--- | :--- | :--- |
| referral\_id | int | referral id |
| referral | name | referral account name |
| staked\_amount | asset | amount of NUT staked |

New table **positionrefs**:  
This table connects positions with referrals so the system knows when and where to pay the fees.  
  
****

| **parameter** | **type** | **description** |
| :--- | :--- | :--- |
| referral\_id | int | referral id |
| position\_id | int | Id of the related position |

  
****New actions related to referral program:

{% tabs %}
{% tab title="referraladd" %}
**Action**

deposit NUT to the **eosdtcntract**, specifying referral in memo. 

**Parameters**

NUT amount  
memo: referral

**Rejections**

When input &lt; referral\_min\_stake  
  
****When missing the referral’s signature

**Example**

```text
cleos transfer ‘{“from”:“testtesttest",“to”:“eosdtcntract”,“quantity”:“2.000000000 NUT”,“memo”:“referral"}’
```
{% endtab %}

{% tab title="referraldel" %}
**Action**

withdraw NUT from the smart contract and delete the referral account name from the table.

**Parameters**

referral\_id

**Rejections**

When missing the referral’s signature for a given referral\_id  
  
****When the referral doesn’t exist in the referrals table \(can’t find referral by id\)

**Example**

```text
cleos push action eosdtcntract referraldel '{"referral_id": "2"}' -p user
```
{% endtab %}

{% tab title="posandrefadd" %}
**Action**

Create an empty position entity and set the caller to be the owner.

**Parameters**

referral\_id

**Rejections**

When missing referral\_id  
  
****When the referral doesn’t exist in the referrals table \(can’t find referral by id\)

  
**Example**

```text
cleos push action eosdtcntract posandrefadd '{"referral_id": "2"}' -p user
```
{% endtab %}
{% endtabs %}



For frontend integrations you can use our widget, which has configurable color schemes \(Check [here](https://partner.eosdt.com/en)\). The widget comes in different forms and may be installed via npm, CDN, or IFrame. Check the [widget repo](https://github.com/equilibrium-eosdt/widget) for additional details.     


