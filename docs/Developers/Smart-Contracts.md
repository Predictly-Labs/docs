# Smart Contracts

## Overview

Predictly uses Move smart contracts on Movement Network.

## Contract Information

| Property | Value                                                                |
| -------- | -------------------------------------------------------------------- |
| Network  | Movement Testnet (Bardock)                                           |
| Address  | `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565` |
| Module   | `predictly::market`                                                  |
| Language | Move                                                                 |

## View Functions

Read-only functions (no gas required):

| Function                | Description         |
| ----------------------- | ------------------- |
| `get_market_count`      | Total markets       |
| `get_market_status`     | Market status       |
| `get_market_outcome`    | Market result       |
| `get_market_pools`      | YES/NO pool amounts |
| `get_percentages`       | YES/NO percentages  |
| `get_participant_count` | Number of voters    |
| `calculate_reward`      | Potential reward    |

## Entry Functions

State-changing functions (gas required):

| Function        | Description                 |
| --------------- | --------------------------- |
| `create_market` | Create new market           |
| `place_vote`    | Vote on market              |
| `resolve`       | Resolve market (judge only) |
| `claim_reward`  | Claim winnings              |

## Constants

| Type       | Values                            |
| ---------- | --------------------------------- |
| Status     | 0=ACTIVE, 1=RESOLVED, 2=CANCELLED |
| Outcome    | 0=PENDING, 1=YES, 2=NO, 3=INVALID |
| Prediction | 1=YES, 2=NO                       |

## TypeScript Integration

```typescript
import { Aptos, AptosConfig } from "@aptos-labs/ts-sdk";

const CONTRACT =
  "0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565";

const config = new AptosConfig({
  fullnode: "https://testnet.movementnetwork.xyz/v1",
});

const aptos = new Aptos(config);
```

## Explorer

[View on Movement Explorer](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet)
