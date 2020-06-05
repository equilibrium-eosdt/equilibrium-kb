# Test framework

Testing smart contracts in the Equilibrium framework is a complicated, tedious process that goes as follows: apart from the contract development team, the QA team writes a node.js application that fully implements the entire business logic of the smart contracts. In other words, we emulate our contracts outside the blockchain. Importantly, we do not emulate their individual parts, but rather the entire overall structure. Then each operation of the test script is executed on the blockchain and in the local application, and we check that both systems act identically and appropriately. This way we minimize the chance of missing complex bugs. The high complexity of the system and cost of errors led us to this approach.

In general, QA in Equilibrium uses deep regression randomized testing dependent on the blackbox strategy:

* **Replay of the system in a local node.js application**. This greatly complicates and slows down testing, but lets us achieve higher quality for the output code. The parallel development of two systems, which should give the same result under the same conditions, lets us see all the possible difficulties and problems in the design of the system at very early stages of testing.  
* **The black box strategy**. We intentionally restrict the QA team from contact with the contract code. Without knowing the internal structure of the system, they can’t transfer design errors to the local node.js application. Therefore only ABI and technical specifications are used when mimicking contracts locally.  
* **Deep regression** is achieved by reconciling all the parameters of the local node.js application with the values ​​from the tables of our contracts in the blockchain. After each action on the contract, a method is called to check that all the local application’s and blockchain contracts’ parameters have the same values. Even indirectly related parameters get checked. So we eliminate the most unpredictable and unlikely mistakes.

## Technical details

Initial testing is carried out on local testers' machines. To do this, a local blockchain node is deployed and test versions of all system contracts are downloaded. Any initial blockchain state required for the test scenario is set. QA specialists manage the time parameter in this blockchain, which lets us correctly test the functionality that is sensitive to the movement of time \(e.g. fees accrual\).

Secondary testing is carried out in the Jungle testnet, where contracts achieve the form in which they will be uploaded to the EOS mainnet. It is no longer possible to change the current time there and freely manage contract parameters. Therefore this stage of testing is used to verify the production version of contracts as well as the correctness of migrations and calculations. The main test scenarios are checked under conditions almost identical to the mainnet.

## About test scripts

All test scenarios are a set of sequential actions in the blockchain and local node.js application that affect all the main functions. What makes our testing stand out is that there are no deterministic values ​​in the scripts. All values ​​for the tests are generated randomly and the action is considered to be completed correctly only if the operation with a random value yields the same results both in the local application and in the blockchain. This means we bring the test conditions as close as possible to the production conditions, and find problems caused by specific values ​​transferred to our contracts.

In addition to testing the correct operation of the actions, all invalid calls are also checked. With their help, we make sure that any incorrect conditions or input parameters will cause the contract to fail execution with an error.

