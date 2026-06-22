# Presale Contract

> One of my first Solidity projects — kept here as a reminder of where it all started.

A token presale smart contract built with Hardhat and Solidity, deployed on the Ethereum Sepolia testnet. It supports purchasing ERC20 tokens using either ETH or USDT, with real-time ETH/USD pricing via Chainlink oracles.

---

## Overview

The `Presale` contract manages a time-bounded token sale. Buyers can purchase tokens with ETH (price calculated live from Chainlink) or USDT (at a fixed USD price). The owner can update the price, withdraw funds, and recover unsold tokens once the presale ends.

A gift code system tracks which referral code drove each purchase — useful for affiliate or campaign analytics.

---

## Tech Stack

- **Solidity** `^0.8.19`
- **Hardhat** — compilation, testing, deployment, gas reporting
- **OpenZeppelin** — ERC20, ReentrancyGuard
- **Chainlink** — ETH/USD price feed (`AggregatorV3Interface`)
- **hardhat-deploy** — scripted multi-network deployments
- **Ethers.js v6** — contract interaction in tests

---

## Contract Features

- Buy tokens with **ETH** — amount calculated from live Chainlink price feed
- Buy tokens with **USDT** — fixed USD price per token
- **Gift code tracking** — each purchase can be tagged with a numeric code
- **Reentrancy guard** on all purchase functions
- **Time-gated** — purchases only accepted between `openingTime` and `closingTime`
- Owner functions: `changePrice`, `changeOwner`, `withdrawEther`, `withdrawToken`, `endPresale`

---

## Project Structure

```
contracts/
├── Presale.sol                  # Main presale contract
├── Mocks/
│   └── MockV3Aggregator.sol     # Chainlink mock for local testing
└── tokens/
    ├── SendingToken.sol         # Token being sold (ST)
    └── ReceivingToken.sol       # Token used as USDT mock (RT)

deploy/
├── 00-deploy-mocks.js
├── 01-deploy-RT.js
├── 02-deploy-ST.js
└── 03-deploy-ICO.js

test/
└── unit.test.js

helper-hardhat.config.js         # Deployment args (price, supply, timing)
```

---

## Getting Started

### Install dependencies

```bash
npm install
```

### Set up environment variables

Copy `.env.example` to `.env` and fill in:

```env
SEPOLIA_RPC_URL=
PRIVATE_KEY=
ETHERSCAN_API_KEY=
COINMARKETCAP_API_KEY=
```

### Run locally

```bash
# Start a local Hardhat node
npx hardhat node

# Deploy to local network
npx hardhat deploy --network hardhat
```

### Run tests

```bash
npx hardhat test
```

### Deploy to Sepolia

```bash
npx hardhat deploy --network sepolia
```

### Verify on Etherscan

Verification runs automatically after Sepolia deployment if `ETHERSCAN_API_KEY` is set.

---

## Deployed Contracts (Sepolia)

| Contract | Address |
|---|---|
| Presale (ICO) | `0xA1F06372Ed2062f24dE85bEdB1C4B90C1900A56e` |
| Hope Token | `0x10fa6759aEc16e20bc7d1ceD9Dc0774C4798DD9e` |

---

## Constructor Parameters

| Parameter | Description |
|---|---|
| `_tokenPrice` | Price per token in USD (scaled by `1e-3`) |
| `_tokensForSale` | Total token supply available for sale |
| `_openingTime` | Unix timestamp for presale start |
| `_closingTime` | Unix timestamp for presale end |
| `_priceFeedAddress` | Chainlink ETH/USD aggregator address |
| `_token` | Address of the ERC20 token being sold |
| `_usdtToken` | Address of the USDT token contract |
| `_owner` | Owner/admin address |

---

## Events

| Event | Emitted when |
|---|---|
| `TokensPurchased(address, uint256)` | A buyer successfully purchases tokens |
| `PresaleEnded(uint256)` | Owner calls `endPresale` after closing time |
| `Withdrawal(address, uint256)` | Owner withdraws ETH from the contract |

---

## License

MIT
