# HardHat project

## Setup

First, we need to set up our Hardhat project. Open terminal and follow these commands.
Make a folder with you project name and go to the project directory.

```shell
mkdir xdc-hardhat
cd xdc-hardhat
```

![1678811043789](https://user-images.githubusercontent.com/7695325/225071607-c2eb8cc5-3ae7-4f50-afe7-eeb4d0e4da98.png)

Initialize the project and install Hardhat.

```shell
npm init --yes
npm install --save-dev hardhat
```

![1678811085814](https://user-images.githubusercontent.com/7695325/225071793-3cd5792d-6b29-4eb8-b71c-75a770ddf719.png)

In the same directory, run:

```shell
npx hardhat
```

Select `Create a Javascript project`:

![1678811229474](https://user-images.githubusercontent.com/7695325/225072430-ba46189d-826d-4452-9033-8d5c79df62af.png)

Press enter for already specified path:

![1678811303660](https://user-images.githubusercontent.com/7695325/225072777-7b6a93a7-ed5f-4e00-bfbf-ffe71aaf065d.png)

Press enter for adding a .gitignore:

![1678811379218](https://user-images.githubusercontent.com/7695325/225073024-2d274b62-f37b-451b-9a0b-4d67843a680a.png)

Press enter for install dependencies with @nomicfoundation/hardhat-toolbox:

![1678811776328](https://user-images.githubusercontent.com/7695325/225074824-5d20a3f4-13fc-4eb2-b68c-ff7a2aed9b51.png)

After install:

![1678811967128](https://user-images.githubusercontent.com/7695325/225075636-b469a703-ee85-45f1-9da7-25b2b8be774d.png)

## Write Smart Contract

Create a new contract `Pizza` under folder `contracts`:

```solidity
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.9;

contract Pizza {
    uint256 public PIZZA_SIZE;
    uint256 public slices;

    constructor(uint256 _pizzaSize) {
        PIZZA_SIZE = _pizzaSize;
        slices = 0;
        slices += PIZZA_SIZE;
    }

    function eatSlice() public {
        require(slices > 0, "No Slices Left. Lets Bake a new Pizza!");
        slices -= 1;
    }

    function bakeNewPizza() public {
        require(slices == 0, "There still slices from a previous Pizza!");
        slices += PIZZA_SIZE;
    }
}
```

## Compile the contract

```shell
npx hardhat compile
```

With below output:

```text
Downloading compiler 0.8.18
Compiled 2 Solidity files successfully
```

## Deployment

### Write deploy script

To write the script to deploy the contract, create deploy.js in scripts folder:

```javascript
const hre = require("hardhat");

async function main() {
  // make sure to change the name of your contract
  const Pizza = await hre.ethers.getContractFactory("Pizza");

  // 4 in the bracket is to give the value to the parameter(_pizzaSize) in the constructor of the smart contract contract.
  const pizza = await Pizza.deploy(4);

  await pizza.deployed();

  console.log(`pizza contract address: xdc${pizza.address.slice(2)}`);
}

// Call the main function and catch if there is any error
main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
```

### Setup .env file

Make a `.env` at the root of the project to store the private key and network url. Make sure you do not push this file to github.

```text
PRIVATE_KEY="4530a91ae8f7418c55b291ef96856d20745722f20787e19db177ea18fe06c721"
```

### Install dotenv

To be able to import env file variables, please install dotenv from terminal:

```shell
npm install dotenv
```

### Edit file hardhat.config.js

Open the hardhat.config.js file. Now we will add the network url and private key of your wallet to this file, so that you can deploy your contract. Your harhat.config.js should look like this:

```js
require("@nomicfoundation/hardhat-toolbox");
require("dotenv").config({ path: ".env" });

const PRIVATE_KEY = process.env.PRIVATE_KEY;

/** @type import('hardhat/config').HardhatUserConfig */
module.exports = {
  solidity: "0.8.18",
  networks: {
    xinfin: {
      url: "https://erpc.xinfin.network",
      accounts: [PRIVATE_KEY],
    },
    apothem: {
      url: "https://erpc.apothem.network",
      accounts: [PRIVATE_KEY],
    },
  },
};
```

### Final Step

After writing code for the setup and contract, go back to terminal. Make sure you are in your project directory and type:

```shell
npx hardhat run scripts/deploy.js --network apothem
```

With output:

```text
pizza contract address: xdc5f5634a4bFBE9296696408a08d5071d89D553361
```
