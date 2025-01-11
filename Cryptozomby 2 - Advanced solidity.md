# My summary
- [x] install `npm` and `node`
```
sudo apt install npm
sudo npm install node
```

- [x] install **Truffle** and make it available globally
```
sudo npm install truffle -g
```

- [x] initialize our new project (to create a set of folders and config files with the following structure).
`truffle init`

- [ ] Since deploying a smart contract requires **Truffle** to sign transactions, we are going to need a tool called `truffle-hdwallet-provider`. Its only purpose is to handle the transaction signing.
`sudo npm install truffle-hdwallet-provider`

- [ ] Install Chai - one of the best assertion modules.
`npm -g install chai`


...

[Testing Against Loom](https://cryptozombies.io/en/lesson/11/chapter/14)

# Chapter 1: Introduction

If you're new to **CryptoZombies**, it's highly recommended that you go over the first six lessons before starting this one. Please take the time to get familiar with writing a smart contract. Otherwise, it will be quite hard to follow this lesson.

## The Missing Piece

So you've worked your way through our previous lessons. Awesome! This means you've almost mastered the art of building DApps.

But an important piece of the puzzle is still missing.

That's right... you still have to learn how to **_deploy a smart contract_**.

Now, if you have a background in front-end development, you are probably well accustomed to the multitude of tools like _Webpack, Gulp, or Browserify_ that make a developer's life simpler.

But what tools do **Solidity** developers use?

## Truffle

**_Truffle_** is the most popular blockchain development framework for good reason - it's packed with lots of useful features:

- easy smart contract compilation
- automated ABI generation
- integrated smart contract testing - there's even support for **Mocha** and **Chai**!
- support for multiple networks - code can be deployed to Rinkeby, **_Ethereum_** or even to **_Loom_**. We'll walk you through this later😉

Provided that `npm` and `node` have been installed on your computer, we'll want you to install **Truffle** and make it available globally.


# Chapter 2: Getting Started with Truffle

Now that we've installed **Truffle**, it's time to initialize our new project by running `truffle init`. All it is doing is to create a set of folders and config files with the following structure:

```
├── contracts
    ├── Migrations.sol
├── migrations
    ├── 1_initial_migration.js
└── test
truffle-config.js
truffle.js
```

Contracts, migrations, tests... this is pretty complicated😟

Don't worry, learning to use **Truffle** won't eat your brains. This chapter will walk you through **Truffle**'s default project structure, and once you know how to use **Truffle**, deploying smart contracts will be a breeze.

## Truffle's Default Directory Structure

So, running the `truffle init` command inside of the `CryptoZombies` directory, should create several directories and some JavaScript and Solidity files. Let's have a closer look:

- **_contracts_**: this is the place where **Truffle** expects to find all our smart contracts. To keep the code organized, we can even create nested folders such as `contracts/tokens`. Pretty neat😉.
    
    > Note: `truffle init` should automatically create a contract called `Migrations.sol` and the corresponding migration file. We'll explain them a bit later.
    
- **_migrations_**: a migration is a JavaScript file that tells **Truffle** how to deploy a smart contract.
    
- **_test_**: here we are expected to put the unit tests which will be JavaScript or Solidity files. Remember, once a contract is deployed it can't be changed, making it essential that we test our smart contracts before we deploy them.
    
- **_truffle.js_** and **_truffle-config.js_**: config files used to store the network settings for deployment. **Truffle** needs two config files because on Windows having both `truffle.js` and `truffle.exe` in the same folder might generate conflicts. Long story short - if you are running Windows, it is advised to delete `truffle.js` and use `truffle-config.js` as the default config file. Check out **Truffle**'s [official documentation](https://truffleframework.com/docs/truffle/reference/configuration) to further your understanding.
    

But why should I use this directory structure? I'm not used to it and it looks complicated...

Well, there's are a few good reasons. First, **Truffle** will not work as expected if you change the names of these folders.

Second, by adhering to this convention your projects will be easily understood by other developers. To put it short, using a standard folder structures and code conventions make it easier if you expand or change your team in the future.

## truffle-hdwallet-provider

In this lesson, we will be using _Infura_ to deploy our code to **_Ethereum_**. This way, we can run the application without needing to set up our own **_Ethereum_** node or wallet. However, to keep things secure, _Infura_ does not manage the private keys, which means it can't sign transactions on our behalf. Since deploying a smart contract requires **Truffle** to sign transactions, we are going to need a tool called `truffle-hdwallet-provider`. Its only purpose is to handle the transaction signing.

> Note: Maybe you are asking why we chose not to install `truffle-hdwallet-provider` in the previous chapter using something like:

```
 npm install truffle truffle-hdwallet-provider
```

Well... the `truffle init` command expects to find an empty directory. If there's any file there, it will error out. Thus, we need to do everything in the correct order and install `truffle-hdwallet-provider` after we run `truffle init`.

# Chapter 3: Compiling the Source Code

Congratulations! Now that we've put the project structure in place and set up `truffle-hdwallet-provider`, let's compile our contracts.

Why do we need to compile, you ask?

The _Ethereum Virtual Machine_ can't directly understand Solidity source code as we write it. Thus, we need to run a compiler that will "translate" our smart contract into machine-readable **_bytecode_**. The virtual machine then executes the bytecode, and completes the actions required by our smart contract.

Curious about how does the bytecode look like? Let's take a look:

```
"0x60806040526010600155600154600a0a6002556201518060035566038d7ea4c6800060085560006009556046600a55336000806101000a81548173ffffffffffffffffffffffffffffffffffffffff021916908373ffffffffffffffffffffffffffffffffffffffff1..."
```

As you can see, a human is about as likely to be able to read bytecode as a real life zombie!

## Using the Solidity Compiler

Now that we're talking about the Solidity compiler, we should mention that the devs managed to bake in some nifty features.

Let's pretend we'd want to modify the definition of the `add` function included in `SafeMath`:

```
function add(uint16 a, uint16 b) internal returns (uint16) {
    uint16 c = a + b;
    assert(c >= a);
    return c;
}
```

If we're going to compile this function, the Solidity compiler will throw a **_warning_**:

```
safemath.sol:110:11: Warning: Function state mutability can be restricted to pure
          function add(uint16 a, uint16 b) internal returns (uint16) {
          ^ (Relevant source part starts here and spans across multiple lines).
```

What the compiler is trying to say is that our function does not read or write from/to the blockchain and that we should use the `pure` modifier.

Why is this important?

Well, making a function `pure` or `view` saves us gas. Since these functions are not going to modify the state of the blockchain, there is no need for miners to execute them. To put it in a few words, `pure` and `view` functions can be `call`ed for free.

## CryptoZombies- The Game

Remember, we've embedded our logic in a smart contract called `ZombieOwnership.sol`.

Hmmm... not a great name for a game.

Fortunately, this isn't a problem. We can use inheritance to create a smart contract with the same actions and features with whatever name we choose.

Let's create a new smart contract named `CryptoZombies` that inherits from `ZombieOwnership.sol`:

```
pragma solidity ^0.4.24;

import "./zombieownership.sol";

contract CryptoZombies is ZombieOwnership
    {

    }
```

Next, we copied all our smart contracts into the `./contracts` folder. Now the project structure should look like this:

```
.
├── contracts
    ├── Migrations.sol
    ├── CryptoZombies.sol
    ├── erc721.sol
    ├── ownable.sol
    ├── safemath.sol
    ├── zombieattack.sol
    ├── zombiefactory.sol
    ├── zombiefeeding.sol
    ├── zombiehelper.sol
    ├── zombieownership.sol
├── migrations
└── test
```

Everything is set up properly. Let's compile our project.
## Put it to the test:

1. Execute `truffle compile`. This command should create the build artifacts and place them in the `./build/contracts` directory.
    
    > Note: The build artifacts are comprised of the "bytecode" versions of the smart contracts, ABIs, and some internal data **Truffle** is using to correctly deploy the code. Avoid editing these files, or **Truffle** might stop working correctly.
    
# Chapter 4: Migrations

Normally at this point, before deploying to **_Ethereum_**, you would want to test your smart contract locally. You can do this using a tool called [Ganache](https://truffleframework.com/ganache), which sets up a local **_Ethereum_** network.

However, while testing is very important, it requires an entire lesson to cover — so we’re just going to stick to deployment in this lesson. If you're so inclined, to learn more about testing, I recommend our very own [Testing Smart Contracts with Truffle](http://cryptozombies.io/en/lesson/10) lesson.

To deploy to **_Ethereum_** we will have to create something called a **migration**.

Migrations are JavaScript files that help **Truffle** deploy the code to **_Ethereum_**. Note that `truffle init` created a special contract called `Migrations.sol` that keeps track of the changes you're making to your code. The way it works is that the history of changes is saved onchain. Thus, there's no way you will ever deploy the same code twice.

## Creating a New Migration

We'll start from the file `truffle init` already created for us- `./contracts/1_initial_migration.js`. Let's take a look at what's inside:

```
var Migrations = artifacts.require("./Migrations.sol");
module.exports = function(deployer) {
  deployer.deploy(Migrations);
};
```

Pretty straightforward, isn't it?

First, the script tells **Truffle** that we'd want to interact with the `Migrations` contract.

Next, it exports a function that accepts an object called `deployer` as a parameter. This object acts as an interface between you (the developer) and **Truffle**'s deployment engine. Even though the `deployer` provides a multitude of useful functions, we won't be using them in the scope of this lesson. Once you've finished, feel free to check out **Truffle**'s [documentation](https://truffleframework.com/docs/truffle/getting-started/running-migrations) if you're inclined to learn more about **Truffle**'s abilities.

To get everything ready for deployment, we've gone ahead and created a new file `./contracts/2_crypto_zombies.js`, and copied and pasted the content from `./contracts/1_initial_migration.js`.

## Put it to the test:

1. Modify `./contracts/2_crypto_zombies.js` to this:

```
var CryptoZombies = artifacts.require("./CryptoZombies.sol");
module.exports = function(deployer) {
  deployer.deploy(CryptoZombies);
};
```

# Chapter 5: Configuration Files

Awesome! You've successfully compiled the source code and created a migration file.

There is still one more thing to do before we can deploy. We'll have to edit the configuration file to tell **Truffle** the networks we want to deploy to.

Wait a minute, I thought there was only one **_Ethereum_** network. What am I missing here?

## Ethereum Test Networks

Several public **_Ethereum_** test networks let you test your contracts for free before you deploy them to the main net (remember once you deploy a contract to the main net it can't be altered). These test networks use a different consensus algorithm to the main net (usually PoA), and Ether is free to encourage thorough testing.

In this lesson, we will be using Rinkeby, a public test network created by The Ethereum Foundation.

## The truffle.js configuration file

Now, let's take a look at the default **Truffle** configuration file:

```
$ cat truffle.js
/*
 * NB: since truffle-hdwallet-provider 0.0.5 you must wrap HDWallet providers in a
 * function when declaring them. Failure to do so will cause commands to hang. ex:
 *
 * mainnet: {
 *     provider: function() {
 *       return new HDWalletProvider(mnemonic, 'https://mainnet.infura.io/<infura-key>')
 *     },
 *     network_id: '1',
 *     gas: 4500000,
 *     gasPrice: 10000000000,
 *   },
 */
```

It is just an empty shell. Thus, we'll be required to update this file to allow us to deploy contracts to Rinkeby and the Ethereum mainnet.

### Truffle's HD Wallet Provider

Remember the second chapter?

We asked you to install an additional package called `truffle-hdwallet-provider` that helps **Truffle** sign transactions.

Now, we want to edit our configuration file to use `HDWalletProvider`. To get this to work we'll add a line at the top of the file:

```
const HDWalletProvider = require("truffle-hdwallet-provider");
```

Next, we'll create a new variable to store our mnemonic:

```
const mnemonic = "onions carrots beans ...";
```

Note that we don't recommend storing secrets like a mnemonic or a private key in a configuration file.

...but why?

Config files are often pushed to GitHub, where anyone can see them, leaving you wide open to attack 😱! To avoid revealing your mnemonic (or your private key!), you should read it from a file and add that file to `.gitignore`. We'll show you how to do this later.

**To keep things simple in this case**, we've copied the mnemonic and stored in a variable.

### Set up Truffle for Rinkeby and Ethereum main net

Next, to make sure **Truffle** "knows" the networks we want to deploy to, we will have to create two separate objects- one for Rinkeby and the other one for the **_Ethereum_** main net:

```
networks: {
  // Configuration for mainnet
  mainnet: {
    provider: function () {
      // Setting the provider with the Infura Mainnet address and Token
      return new HDWalletProvider(mnemonic, "https://mainnet.infura.io/v3/YOUR_TOKEN")
    },
    network_id: "1"
  },
  // Configuration for rinkeby network
  rinkeby: {
    // Special function to setup the provider
    provider: function () {
      // Setting the provider with the Infura Rinkeby address and Token
      return new HDWalletProvider(mnemonic, "https://rinkeby.infura.io/v3/YOUR_TOKEN")
    },
    // Network id is 4 for Rinkeby
    network_id: 4
  }
```

> Note: the provider value is wrapped in a function, which ensures that it won't get initialized until it's needed.

### Wrapping it up

Now, let's put these pieces together and have a look at how our config file should look:

```
// Initialize HDWalletProvider
const HDWalletProvider = require("truffle-hdwallet-provider");

// Set your own mnemonic here
const mnemonic = "YOUR_MNEMONIC";

// Module exports to make this configuration available to Truffle itself
module.exports = {
  // Object with configuration for each network
  networks: {
    // Configuration for mainnet
    mainnet: {
      provider: function () {
        // Setting the provider with the Infura Mainnet address and Token
        return new HDWalletProvider(mnemonic, "https://mainnet.infura.io/v3/YOUR_TOKEN")
      },
      network_id: "1"
    },
    // Configuration for rinkeby network
    rinkeby: {
      // Special function to setup the provider
      provider: function () {
        // Setting the provider with the Infura Rinkeby address and Token
        return new HDWalletProvider(mnemonic, "https://rinkeby.infura.io/v3/YOUR_TOKEN")
      },
      // Network id is 4 for Rinkeby
      network_id: 4
    }
  }
};
```

# Chapter 6: Deploying Our Smart Contract

Great! That was the difficult part — actually deploying to Rinkeby is going to be straightforward. To do this, **Truffle** relies on something called a **_migration_**.

## Migrations

It sounds like a migration involves a lot of stuff moving around, but in practice, a migration is nothing more than a JavaScript file that tells **Truffle** how to modify the state of our smart contracts.

Obviously, the first migration will just deploy the smart contract. Some other migrations deploy a new version of the code to add features or fix bugs.

In a nutshell, migrations provide a convenient way to keep track of the changes you make to your code.

If you want to deploy more than one contract, a separate migration file must be created for each contract. Migrations are always executed in order- 1,2,3, etc.

In this lesson, we will deploy only to Rinkeby. Deployment to the main net would require real money to pay for the gas and, once deployed, we won't be able to modify the code. Hence, it's best to deploy to Rinkeby first, and thoroughly test the code.

## Get some Ether

Before doing the deployment, make sure there is enough Ether in your account. The easiest way to get Ether for testing purposes is through a service known as a `faucet`. We recommend the [Authenticated Faucet](https://faucet.rinkeby.io/) running on Rinkeby. Follow the instructions, and within a few minutes, your address will be credited with some Ether.

## Put it to the test

1. Now that everything is set up, it's time to deploy to Rinkeby. To do so, run `truffle migrate --network rinkeby` in the terminal to the right. Note how migrations are being executed in order😉.
    
    > Note: `truffle deploy` is just an alias for `truffle migrate`. However, since our command-line interpreter is pretty basic, it won't consider the answer correct unless you're using `migrate`.
    

Deployment to the main net is not complicated at all. Once the smart contract is tested, you'll only have to run: `truffle migrate --network mainnet`. Don't forget that you'll have to pay for gas! We trust you'll be able to do it.

If everything goes well, you're going to see a response that's similar to the one to the right.
# Chapter 7: Use Truffle with Loom!

It might not seem much, but you just deployed the `CryptoZombies` smart contract!

Even if **Truffle** helped a lot, this is no small feat, so pat yourself on the back.

## Loom Basechain

Now, if you want to build DApps on **_Ethereum_**, there's one thing you should be aware of - on the main net, users are required to **_pay gas fees for every transaction_**. But this isn't ideal for a user-facing DApp or a game. It can easily ruin the user experience.

Conversely, on **_Loom_**, your users can have much speedier and gas-free transactions, making it a much better fit for games and other non-financial applications.

This means that your **_Loom_** zombies will be fast zombies!

That's not all - deploying to **_Loom_** is no different from deploying to Rinkeby, or to the Ethereum main net. If you know how to do one, you also know how to do the other.

In the next chapters, we'll be walking you through deploying to **_Loom_**.

## loom-truffle-provider

We at **_Loom_** are using **Truffle** to build, test, and deploy our smart contracts. To make our life easier, we developed something called a **_provider_** that lets Truffle deploy to **_Loom_** just like it deploys to Rinkeby or Ethereum main net.

Without delving too much into details, the provider works like a bridge that makes Web3 calls compatible with **_Loom_**. The beauty of it is that, to use it, you don't have to understand how it works under the hood.

##
# Chapter 8-9: Deploy to Loom Testnet

In this chapter, we’re going to deploy our smart contract to the **_Loom_** Testnet, but before doing the deployment, some prep work is needed.

First, we should create our own **_Loom_** private key. The easiest way to do it is by downloading and installing **_Loom_** according to this [tutorial](https://loomx.io/developers/en/basic-install-all.html).

Next, creating a private key is as simple as this:

```
$./loom genkey -a public_key -k private_key
local address: 0x42F401139048AB106c9e25DCae0Cf4b1Df985c39
local address base64: QvQBE5BIqxBsniXcrgz0sd+YXDk=
$cat private_key
/i0Qi8e/E+kVEIJLRPV5HJgn0sQBVi88EQw/Mq4ePFD1JGV1Nm14dA446BsPe3ajte3t/tpj7HaHDL84+Ce4Dg==
```

> Note: Never reveal your private keys! We are only doing this for simplicity's sake.

## Updating truffle.js

The first thing we are required to do is to initialize `loom-truffle-provider`. The syntax is similar to the one we've already used for `HDWalletProvider`:

```
const LoomTruffleProvider = require('loom-truffle-provider');
```

Next, just as we did in chapter 5, we'll have to let **_Truffle_** know how to deploy on **_Loom_** testnet. To do so, let's add a new object to `truffle.js`

```
loom_testnet: {
  provider: function() {
    const privateKey = 'YOUR_PRIVATE_KEY'
    const chainId = 'extdev-plasma-us1';
    const writeUrl = 'http://extdev-plasma-us1.dappchains.com:80/rpc';
    const readUrl = 'http://extdev-plasma-us1.dappchains.com:80/query';
    return new LoomTruffleProvider(chainId, writeUrl, readUrl, privateKey);
    },
  network_id: '9545242630824'
}
```

## Put it to the test:

1. Add the line of code that initializes `LoomTruffleProvider`.
    
2. Place the configuration for `loom_testnet` near the bottom of the file.Put it to the test:

3. We've made `loom-truffle-provider` available as an `npm` package. Let's install it.
    
    > Note: This time, there's no need to make the package available globally.
    
4. Run `truffle migrate --network loom_testnet` and check the output.

Yeah, you've made it look simple!💪🏻

# Chapter 10: Deploy to Basechain

Congratulations! You've successfully deployed to **_Loom_** Testnet. But can you guess what's coming next?🤔

Yup, you're right! This chapter will walk you through the process of deploying to **_Basechain_** (that is our Mainnet).

Here's a brief rundown of what you'll do in this chapter:

- Create a new private key.
- Creating a new private key is pretty straightforward. But since we're talking about deploying to the main net, it's time to get more serious about security. Thus, we'll show you how to securely pass the private key to **Truffle**.
- Tell **Truffle** how to deploy to **_Basechain_** by adding a new object to the `truffle.js` configuration file.
- Whitelist your deployment keys so you can deploy to **_Basechain_**.
- Lastly, we wrap everything up by actually deploying the smart contract.

### Create a new private key

You already know how to create a private key. However, we must change the name of the file in which we're going to save it:

```
./loom genkey -a mainnet_public_key -k mainnet_private_key
local address: 0x07419790A773Cc6a2840f1c092240922B61eC778
local address base64: B0GXkKdzzGooQPHAkiQJIrYex3g=
```

### Securely pass the private key to Truffle

The next thing we want to do is to prevent the private key file from being pushed to GitHub. To do so, let's create a new file called `.gitignore`:

```
touch .gitignore
```

Now let's "tell" GitHub that we want it to ignore the file in which we saved the private key by entering the following command:

```
echo mainnet_private_key >> .gitignore
```

Now that we made sure our secrets aren't going to be pushed to GitHub, we must edit the `truffle.js` configuration file and make it so that **Truffle** reads the private key from this file.

Let's start by importing a couple of things:

```
const { readFileSync } = require('fs')
const path = require('path')
const { join } = require('path')
```

Next, we would want to define a function that reads the private key from a file and initializes a new `LoomTruffleProvider`:

```
function getLoomProviderWithPrivateKey (privateKeyPath, chainId, writeUrl, readUrl) {
  const privateKey = readFileSync(privateKeyPath, 'utf-8');
  return new LoomTruffleProvider(chainId, writeUrl, readUrl, privateKey);
}
```

Pretty straightforward, isn't it?

### Tell Truffle how to deploy to Basechain

Now, we must let **Truffle** know how to deploy to **_Basechain_**. To do so, let's add a new object to `truffle.js`

```
basechain: {
  provider: function() {
    const chainId = 'default';
    const writeUrl = 'http://basechain.dappchains.com/rpc';
    const readUrl = 'http://basechain.dappchains.com/query';
    const privateKeyPath = path.join(__dirname, 'mainnet_private_key');
    const loomTruffleProvider = getLoomProviderWithPrivateKey(privateKeyPath, chainId, writeUrl, readUrl);
    return loomTruffleProvider;
    },
  network_id: '*'
}
```

At this point, your `truffle.js` file should look something like the following:

```
// Initialize HDWalletProvider
const HDWalletProvider = require("truffle-hdwallet-provider");

const { readFileSync } = require('fs')
const path = require('path')
const { join } = require('path')


// Set your own mnemonic here
const mnemonic = "YOUR_MNEMONIC";

function getLoomProviderWithPrivateKey (privateKeyPath, chainId, writeUrl, readUrl) {
  const privateKey = readFileSync(privateKeyPath, 'utf-8');
  return new LoomTruffleProvider(chainId, writeUrl, readUrl, privateKey);
}

// Module exports to make this configuration available to Truffle itself
module.exports = {
  // Object with configuration for each network
  networks: {
    // Configuration for mainnet
    mainnet: {
      provider: function () {
        // Setting the provider with the Infura Rinkeby address and Token
        return new HDWalletProvider(mnemonic, "https://mainnet.infura.io/v3/YOUR_TOKEN")
      },
      network_id: "1"
    },
    // Configuration for rinkeby network
    rinkeby: {
      // Special function to setup the provider
      provider: function () {
        // Setting the provider with the Infura Rinkeby address and Token
        return new HDWalletProvider(mnemonic, "https://rinkeby.infura.io/v3/YOUR_TOKEN")
      },
      // Network id is 4 for Rinkeby
      network_id: 4
    },

    basechain: {
      provider: function() {
        const chainId = 'default';
        const writeUrl = 'http://basechain.dappchains.com/rpc';
        const readUrl = 'http://basechain.dappchains.com/query';
        const privateKeyPath = path.join(__dirname, 'mainnet_private_key');
        const loomTruffleProvider = getLoomProviderWithPrivateKey(privateKeyPath, chainId, writeUrl, readUrl);
        return loomTruffleProvider;
        },
      network_id: '*'
    }
  }
};
```

### Whitelist your deployment keys

Before deploying to **_Basechain_**, you need to whitelist your keys by following the instructions from our [Deploy to Mainnet](https://loomx.io/developers/en/deploy-loom-mainnet.html) guide. Don't worry about it right now, but keep in mind that you have to do this after you finish this tutorial.

We've gone ahead and performed all these steps and now we are ready to deploy to **_Basechain_**!

# Put it to the test:

1. Run `truffle migrate --network basechain`.

Awesome, you just deployed your smart contract to Basechain!👏🏻