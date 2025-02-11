# **Celo Composer: The Ultimate Guide on How to Use it**

## Introdcution
In this article, you will learn about the following concepts;
- What is Celo blockchain?
- What is Celo Composer?
- How to create, test and deploy smart contracts.

<!-- ## Prerequisites -->

<!-- Before we begin, make sure you have the following installed on your computer:

- Node.js and NPM
- Hardhat
- Celo Wallet -->

So now, let's delve in.

### What is Celo?
**[Celo](https://docs.celo.org/learn/celo-overview)** is a mobile-first blockchain that makes decentralized financial (DeFi) tools and services accessible to anyone with a mobile phone. It aims to break down barriers by bringing the powerful benefits of DeFi to the users of the 6 billion smartphones in circulation today.

It is designed to provide a stablecoin (cUSD) that is pegged to the US dollar, as well as a suite of decentralized applications (dApps) that can be used to access financial services such as remittances, loans, and micropayments.

### What is Celo Composer?
**[Celo Composer](https://github.com/celo-org/celo-composer)** is a tool built by the Celo team that allows you to quickly build, deploy, and iterate on decentralized applications using Celo. It provides a number of frameworks, examples, and Celo specific functionality to help you get started with your next dApp.

### How to Create, Test and Deploy smart contracts with Celo Composer

#### Getting Started with Celo Composer

You'll need to install the Celo Composer CLI, which is a command-line interface that interacts with the Celo network in other to get started. This is because Celo Composer is an open-source tool that is available on Github.

But firstly, ensure that you have **[NodeJS](https://nodejs.org/en/download)** and **[NPM](https://www.npmjs.com/)** installed on your computer. Next, open a terminal and run the following command to install the Composer CLI:

`npm install -g @celo/contractkit`

You can run the command below to verify that the CLI is installed correctly, once the installation is complete:

`celocli`

This command should output the list of available commands for the CLI.

Next, you'll need to install Hardhat. You can install Hardhat using the following command:

`npm install --save-dev hardhat`

#### How to Create a New Project 

To create a new project, you can use the following command:

`celocli init`

The above command will create a new project with the basic files and directories required to create and deploy smart contracts.

The project structure will look like this:
```
contracts
migrations
test
hardhat-config.js
```
The contracts directory contains the smart contract files that you'll create using Celo Composer. The migrations directory will contain migration scripts that are used to deploy the smart contracts to the Celo network. The test directory will contain the tests for the smart contracts.

Next, you'll need to configure Hardhat. To do this, create a new file called `hardhat.config.js` in the root directory of your project with the following code:

```perl
require("@nomiclabs/hardhat-waffle");
require("@nomiclabs/hardhat-ethers");

module.exports = {
    solidity: "0.8.0",
    networks: {
        hardhat: {},
    },
};
```
The above configuration file specifies the Solidity version and sets up the Hardhat network.

#### Creating a Smart Contract

To create a new smart contract, you can use the following command:

`celocli create:contract`

This command will prompt you to enter the name of the smart contract and the directory where you want to create it.

Once you enter the details, Celo Composer will create a new smart contract file with the given name in the specified directory.

For the sake of this article, the smart contract file will contain the basic structure of a Celo smart contract and a sample function.

```solidity
pragma solidity ^0.8.0;

contract MyContract {
    uint256 public myVariable;

    function setMyVariable(uint256 _myVariable) public {
        myVariable = _myVariable;
    }
}
```

In this example, the smart contract is called `MyContract`. It has a public variable called `myVariable` and a function called `setMyVariable` that sets the value of the myVariable variable.

#### Testing the Smart Contract with Hardhat

Before deploying the smart contract to the Celo network, you'll need to test it to ensure that it works as expected. But first, you need to install some additional dependencies to enable Hardhat to interact with the Celo network. You'll need to install `@celo/contractkit` and `@nomiclabs/hardhat-etherscan` as follows:

`npm install --save-dev @celo/contractkit @nomiclabs/hardhat-etherscan`

Once these dependencies have been successfully installed, you can create a new test file in the `test` directory of our project, called `mycontract.test.js`.

```javascript
const { expect } = require("chai");
const { ethers } = require("hardhat");
const { Contract } = require("@ethersproject/contracts");
const { createFixtureLoader } = require("hardhat-ethers");

describe("MyContract", function () {
  let contract;
  let accounts;

  const fixture = async () => {
    const [deployer, ...accounts] = await ethers.getSigners();

    const MyContract = await ethers.getContractFactory("MyContract", deployer);
    const contract = await MyContract.deploy();
    await contract.deployed();

    return {
      contract,
      deployer,
      accounts,
    };
  };

  const loadFixture = createFixtureLoader([ethers.provider]);

  beforeEach(async function () {
    ({ contract, deployer, accounts } = await loadFixture(fixture));
  });

  it("should set the variable correctly", async function () {
    const newValue = 42;
    await contract.setMyVariable(newValue);
    expect(await contract.myVariable()).to.equal(newValue);
  });
});
```

Let's now go through this code step by step:
1. You require some dependencies: `chai` and `ethers` from Hardhat,
    `@ethersproject/contracts`, and `hardhat-ethers`.

2. You describe a new test suite called "MyContract".
3. You declare some variables: `contract` to hold an instance of our `MyContract` contract, and `accounts` to hold the array of signers.
4. You declare a `fixture` function, which returns the contract instance and signers array. This function is called once per test case.
5. You declare a `loadFixture` function that loads the fixture for each test case.
6. You use `beforeEach` to load the fixture and assign the `contract` and `accounts` variables.
7. You declare a single test case, which sets a new value for the variable and then checks that it was set correctly.
8. In the test case, you call the `setMyVariable` function on the `contract` instance, passing in a new value. We then use the `expect `function from `chai` to check that the value of the `myVariable` variable is equal to the new value.

You will then use the following command to run the test:

`npx hardhat test`

This will run the `mycontract.test.js` test file and output the results.

#### Deploying the Smart Contract to the Celo Network

Now that you've tested the smart contract and confirmed that it works as expected, you can deploy it to the Celo network using Celo Composer.

But first, you'll need to make sure that you have the Celo network configuration set up in your `hardhat.config.js` file. You can do this by adding the following code:

```javascript
require("@nomiclabs/hardhat-waffle");
require("@nomiclabs/hardhat-etherscan");
require("@celo/hardhat-plugin");

const mnemonic = process.env.MNEMONIC;

module.exports = {
  solidity: {
    compilers: [
      {
        version: "0.8.4",
      },
    ],
  },
  networks: {
    hardhat: {},
    alfajores: {
      url: "https://alfajores-forno.celo-testnet.org",
      accounts: { mnemonic: mnemonic },
      gasPrice: 1000000000,
    },
    mainnet: {
      url: "https://forno.celo.org",
      accounts: { mnemonic: mnemonic },
      gasPrice: 1000000000,
    },
  },
  etherscan: {
    apiKey: process.env.ETHERSCAN_API_KEY,
  },
  celo: {
    chainId: 44787,
    from: "0x<YOUR_CELO_ADDRESS>",
    gas: 10000000,
    gasPrice: 1000000000,
  },
};
```

This sets up the `alfajores` and `mainnet` networks, which we can use to deploy our contract to the Celo network.

Once you have a Celo account set up, you can use Celo Composer to deploy your `MyContract` contract to the Celo network.

Compile the contract using the following command:

`celocli compile`

Deploy the contract to the Celo network using the following command:

`celocli deploy MyContract --from <YOUR_CELO_ADDRESS>`

Confirm the deployment on the Celo network using the following command:

`celocli contract:show MyContract`


Congratulations! You have successfully deployed your `MyContract` contract to the Celo network using Celo Composer. You can now interact with your contract using the celocli command line tool or any other Celo-compatible wallet or application.


