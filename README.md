![ICO Wizard DApp](https://forum.oracles.org/uploads/default/original/1X/a06ee82b46666b06986a84d648073543fd324b8d.jpg)

# ICO Wizard DApp

[![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](https://raw.githubusercontent.com/hyperium/hyper/master/LICENSE)
[![Build Status](https://travis-ci.org/oraclesorg/ico-wizard.svg?branch=master)](https://travis-ci.org/oraclesorg/ico-wizard)

## Introduction

A quote: `ICO tools should be available for non-coders for free. Raising funds from a crowd is our basic human right.`

ICO wizard is a tool to create token and crowdsale contracts in five simple steps.  Wizard is based on  [TokenMarket](https://github.com/TokenMarketNet/ico) contracts. Wizard is baked how we like it: decentralized, client side, serverless, open source, free, awesome.

ICOs usually have two or more contracts. One token contract and one or more crowdsale contract plus supplemental contracts, e.g., safe math, pricing strategy, etc. Most token contracts are the same (ERC-20); most crowdsale contracts are different.  Token implementation should be stable for compatibility, and it is crucial to connect token to exchanges and wallets. Crowdsale contracts on another side should follow fashion and differentiate a project from others, e.g., create a new type of [FOMO](http://www.urbandictionary.com/define.php?term=fomo), fear of missing out.


## Demo

You can try it on [https://wizard.oracles.org](https://wizard.oracles.org) or watch a demo

[![Demo](https://img.youtube.com/vi/Hvs1gICcXGU/0.jpg)](https://www.youtube.com/watch?v=Hvs1gICcXGU)

## Requirements

- Google Chrome, Chromium, or Google Chrome Canary
- [Metamask](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn). Ethereum web wallet for Google Chrome. Must have to publish contracts to the network.
- Kovan testnet coins.
    -  Create an account in Metamask, copy it's address and ask for free coins on [Kovan's gitter](https://gitter.im/kovan-testnet/faucet)
- Parity for manual operations, e.g., adding people to whitelist after creation of crowdsales, finalizing, etc.
    -  [How to install Parity](https://github.com/paritytech/parity/wiki/Setup)
    - Sync it. `parity --chain kovan --warp ui`
    - Export private key from Metamask and import in to Parity in UI.
- Patience. Metamask doesn't support bulk transactions. Simplest crowdsale will ask you to sign ~10 transactions. 
## Strategy

At the moment, the Wizard supports one type of crowdsale contract, the mighty "whitelisted with tiers and cap". This strategy is popular in modern ICOs due to regulatory involvement in the process (September 2017). A native approach to comply with regulation is to perform KYC of buyers and restrict participants from democratic countries, e.g., the U.S. or PRC. 

Features of "Whitelisted with tiers and cap" strategy:
- Tiers. A crowdsale can have one or more tiers. Each tier has a set of configurable parameters:
    -  Wallet address. Collected funds will be sent to the wallet address immediately after receiving from a participant of the crowdsale.
    -  Start time. A time when a tier of crowdsale will start. You can't set up time in the past. 
    -  End time. A time when a tier of crowdsale will end.
    -  Rate. Exchange rate for a token to the ether. E.g., exchange rate 100 means that for one ether you can buy 100 tokens.
    -  Supply. The maximum amount of tokens available to buy in a tier. Max cap of the crowdsale equals to the sum of all supply of all tiers.
    -  Allow modifying. Controversial feature aka "Box of Pandora". It allows owners to modify Start time, End time, Rate, and Supply after the crowdsale. 
- Whitelist. A crowdsale can have one or more whitelisted addresses. If there are no whitelisted addresses than nobody can buy tokens on that tier. Whitelists are inherited. E.g., if a user was on Tier 1 and didn't buy max cap on Tier 1, he can buy on Tier 2, and other tiers.  Can be updated after the creation of the crowdsale using parity client. Each whitelist has a set of configurable parameters:
    -  Address. Whitelisted ethereum address.
    -  Min. The minimum amount of tokens to buy.
    -  Max. The maximum amount of tokens to buy. 

## How to run

Open [https://wizard.oracles.org](https://wizard.oracles.org) and follow steps.

### Run local version for development

For dev purposes you can build and run ICO Wizard on your machine.

```
git clone https://github.com/oraclesorg/ico-wizard.git wiz
cd wiz
git submodule update --init --recursive --remote
npm install
npm start
```

Go to [localhost:3000](http://localhost:3000) and look around the app!

### ICO configuration
If you are the owner of ICO instance, you can add parameters of created ICO to config (`./src/components/Common/config.js`) for [security reasons](https://github.com/oraclesorg/ico-wizard/issues/133).
Demo config is commented in the file itself.
```
const networks = {
    mainnet: 1,
    morden: 2,
    ropsten: 3,
    rinkeby: 4,
    kovan: 42,
    oraclesTest: 12648430
}

/*
DEMO CONFIG:
export const ICOConfig = { 
    crowdsaleContractURL: '0xc5a21a3e32d9d614ed46e4b2352670fcb21009ee',
    networkID: networks.kovan
};
*/

export const ICOConfig = { 
    
};
```

### Tests

ICO wizard contracts tests are written in javascript with [Truffle framework](http://truffleframework.com/). 
Contracts are deploying in [testrpc](https://github.com/ethereumjs/testrpc) with the script.
Test scripts are added to Travis CI and executed at every pull request.

If you want to start it by yourself, simply run the command `npm test` from the root directory. 
It will start testrpc at port 8545, Truffle will compile, deploy contracts to it and Truffle will execute tests. 

Currently available tests cover the following functionality:

```
Contract: CrowdsaleTokenExt
    ✓ should get absolute reserved tokens for investor
    ✓ should get reserved tokens in percentage for investor
    ✓ should get mint agent: crowdsale contract
    ✓ should get mint agent: NullFinalizeAgentExt contract
    ✓ should get mint agent: ReservedTokensFinalizeAgent contract
    ✓ should get release agent
    ✓ should get owner
  Contract: FlatPricingExt
    ✓ should get last crowdsale tier for pricing strategy contract
    ✓ should return rate of pricing strategy contract
  Contract: MintedTokenCappedCrowdsaleExt
    ✓ should get last crowdsale tier for crowdsale contract
    ✓ should get finalize agent (115ms)
    ✓ should get early participant white list (43ms)
    ✓ should get early participant white list minCap (38ms)
    ✓ should get early participant white list maxCap (44ms)
    ✓ shouldn't accept investment from not whitelisted user (87ms)
    ✓ shouldn't accept investment from whitelisted user less than minCap (108ms)
    ✓ shouldn't accept investment from whitelisted user more than maxCap (109ms)
    ✓ should accept buy from whitelisted user within cap range (209ms)
    ✓ should return updated balance of multisig (104ms)
    ✓ should return token's balance we have bought in previous step
    ✓ should accept buy less than minCap at second buy (204ms)
    ✓ should return updated balance of multisig (103ms)
    ✓ should accept buy of fractioned amount of tokens from whitelisted user within cap range (172ms)
    ✓ should return token balance we have bought in previous step
    ✓ should return updated balance of multisig (105ms)
    ✓ shouldn't accept investment from whitelisted user that exceeds maxCap (96ms)
    ✓ should set endsAt for crowdsale (47ms)
    ✓ should get state for crowdsale (84ms)
    ✓ should get state for crowdsale (91ms)
    ✓ should get state for crowdsale (90ms)
    ✓ should get state for crowdsale (84ms)
    ✓ should get state for crowdsale (123ms)
    ✓ should get state for crowdsale (95ms)
    ✓ should get state for crowdsale (89ms)
    ✓ should get state for crowdsale (95ms)
    ✓ should get state for crowdsale (88ms)
    ✓ should get state for crowdsale (84ms)
    ✓ should finalize crowdsale (249ms)
    ✓ should return updated token balance of user include reserved tokens
  Contract: NullFinalizeAgentExt
    ✓ 
  Contract: ReservedTokensFinalizeAgent
    ✓ 
  Contract: SafeMathLibExt
    ✓ should accurately multipliy numbers
    ✓ should accurately divide numbers
    ✓ should accurately substract numbers
    ✓ should accurately add numbers
  45 passing (4s)
```

## Projects built on ICO Wizard

None! Send PR if you are the first.

## Contributors guide

Issues which are looking for a handsome contributors are marked as _LookingForContributor_ label in [Issues](https://github.com/oraclesorg/ico-wizard/issues?q=is%3Aissue+is%3Aopen+label%3ALookingForContributor)  section of the GitHub 

## Notable Contributors

Brought to you by [Oracles Network](https://oracles.org/team) team.

We appreciate contributors from the community:

- Jeff Christian
- Roman Storm
