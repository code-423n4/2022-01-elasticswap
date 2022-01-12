# ✨ So you want to sponsor a contest

This `README.md` contains a set of checklists for our contest collaboration.

Your contest will use two repos: 
- **a _contest_ repo** (this one), which is used for scoping your contest and for providing information to contestants (wardens)
- **a _findings_ repo**, where issues are submitted. 

Ultimately, when we launch the contest, this contest repo will be made public and will contain the smart contracts to be reviewed and all the information needed for contest participants. The findings repo will be made public after the contest is over and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the contest sponsor (⭐️)**.

---

# Contest setup

## ⭐️ Sponsor: Provide contest details

Under "SPONSORS ADD INFO HERE" heading below, include the following:

- [X] Name of each contract and:
  - [X] lines of code in each
  - [X] external contracts called in each
  - [X] libraries used in each
- [X] Describe any novel or unique curve logic or mathematical models implemented in the contracts
- [X] Does the token conform to the ERC-20 standard? In what specific ways does it differ?
- [X] Describe anything else that adds any special logic that makes your approach unique
- [X] Identify any areas of specific concern in reviewing the code
- [X] Add all of the code to this repo that you want reviewed
- [X] Create a PR to this repo with the above changes.

---

# ⭐️ Sponsor: Provide marketing details

- [X] Your logo (URL or add file to this repo - SVG or other vector format preferred) - See assets folder
- [x] Your primary Twitter handle - https://twitter.com/elasticswap
- [x] Any other Twitter handles we can/should tag in (e.g. organizers' personal accounts, etc.) - 
- [x] Your Discord URI - https://discord.gg/elasticswap
- [x] Your website - https://elasticswap.org/
---

# Contest prep

## ⭐️ Sponsor: Contest prep
- [X] Make sure your code is thoroughly commented using the [NatSpec format](https://docs.soliditylang.org/en/v0.5.10/natspec-format.html#natspec-format).
- [X] Modify the bottom of this `README.md` file to describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the C4 Wardens should keep in mind when reviewing. ([Here's a well-constructed example.](https://github.com/code-423n4/2021-06-gro/blob/main/README.md))
- [X] Please have final versions of contracts and documentation added/updated in this repo **no less than 8 hours prior to contest start time.**
- [X] Ensure that you have access to the _findings_ repo where issues will be submitted.
- [X] Promote the contest on Twitter (optional: tag in relevant protocols, etc.)
- [X] Share it with your own communities (blog, Discord, Telegram, email newsletters, etc.)
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] Designate someone (or a team of people) to monitor DMs & questions in the C4 Discord (**#questions** channel) daily (Note: please *don't* discuss issues submitted by wardens in an open channel, as this could give hints to other wardens.)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# ElasticSwap contest details
- $47,500 main award pot
- $2,500 gas optimization award pot
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2022-01-elasticswap-contest/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts January 20, 2022 00:00 UTC
- Ends January 26, 2022 23:59 UTC

# Overview
ElasticSwap is the first Automated Market Maker (AMM) built explicitly to support elastic supply tokens. Our goal is to provide
a familiar AMM experience to users that supports the many newly released rebasing tokens.  Previous AMMs, like Uniswap have
not provided workable solutions to rebasing token or have even advised protocols from creating them. 

For example the Uniswap [V2 Docs](https://docs.uniswap.org/protocol/V2/reference/smart-contracts/common-errors#rebasing-tokens) have this warning:

```
While positive rebalancing does not break any functionality of Uniswap, those interested in them should be aware that the positive balance found in any pair will be freely available for taking.
```

While supplying liquidity in a Uniswap V2 pool, liquidity providers are losing out on any rebasing that occurs and leaving it up for grabs for anyone.

We have solved this problem, allowing liquidity providers to receive their expected rebases while still providing liquidity in our pools. 

# Contracts

| Contract | SLOC | External Contracts Called | Libraries Used|
|----------|------|---------------------------|---------------|
| Exchange.sol | 326 | ERC20.sol | MathLib.sol, OpenZeppelin |
| ExchangeFactory.sol | 85 | NA | OpenZeppelin |
| MathLib.sol | 709 | NA | NA |

#### Important Notes:

 - **All source code in src/contracts/mocks is explicitly out of scope and is used for testing only**

 - **Fee on Transfer Tokens are NOT supported in our current implementation**

## Protocol Vocabulary

**Exchange** - a single instance of our amm that represents a `Base Token` and `Quote Token` Pair.

**Base Token** - an arbitrary ERC20 token that may be a token with elastic supply / rebases. (think sOHM, sKLIMA, etc)

**Quote Token** - an arbitrary ERC20 token that should be a token that does not rebase / has fixed supply.

**Decay** - the result of the imbalance in tokens that occurs immediately after a rebase occurs in the `Base Token`. See our math document below for more information on this important concept. 


# ElasticSwap Math

Our novel AMM approach is made possible by a mathematical model that ensures equality among all liquidity providers in the light of
tokens that do not have a fixed supply. The math that allows for this functionality is outlined in this [document](https://github.com/ElasticSwap/elasticswap/blob/develop/ElasticSwapMath.md). Please review the examples in this document to understand the math around how our unique AMM works.

# Running Tests
We have developed this protocol using HardHat and there is extensive test coverage that should
help Wardens understand functionality and also probe at potential issues.

To run tests please see instructions in the README.md inside of ./elasticswap


# Areas of concern
1. Price manipulation - Like most AMMs we utilize the constant product formula of `x*y=k`. Any ability to execute a swap that doesn't occur along this curve would be obviously problematic and something we would ask Wardens to spend extra time considering
1. Fair distribution of LP tokens - Our AMMs allows for single sided liquidity to be added in the presence of decay.  When this occurs, LPers are provided LP tokens that represent their share of the pool.  If they were to immediately exit after providing single sided liquidity, they would still be issued both tokens upon exit. We have worked through several scenarios there with the math and believe it always comes out to a "fair" outcome for all liquidity providers.  This would be a great area to double check our thinking on and ensure that this cannot be manipulated or gamed. Also worth considering is when LPers are exiting from their positions when decay is present in the system as well. 
