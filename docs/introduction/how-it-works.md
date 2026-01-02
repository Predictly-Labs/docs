# How It Works

## Overview

Predictly operates in four main steps:

1. **Create** - Market created off-chain (free)
2. **Vote** - Users stake MOVE on YES/NO
3. **Resolve** - Judge determines outcome
4. **Claim** - Winners claim rewards

## Step 1: Create Market

Anyone can create a prediction market.

**Process:**

1. Connect wallet
2. Fill market details (title, description, deadline)
3. Submit (no gas fees)

**Result:**

- Market stored in database
- Status: PENDING

## Step 2: Initialize

Backend deploys market to blockchain.

**Process:**

1. Backend detects new market
2. Deploys to Movement Network
3. Backend pays gas fees

**Result:**

- Market active on blockchain
- Status: ACTIVE

## Step 3: Vote

Users stake MOVE tokens on predictions.

**Process:**

1. Select market
2. Choose YES or NO
3. Enter stake amount
4. Sign transaction

## Step 4: Resolve

Judge determines the outcome after deadline.

| Outcome | Effect            |
| ------- | ----------------- |
| YES     | YES voters win    |
| NO      | NO voters win     |
| INVALID | Everyone refunded |

## Step 5: Claim

Winners claim their rewards.

**Reward Calculation:**

```
Your Reward = (Your Stake / Total Winning Stake) × Total Losing Stake
```

## Hybrid Architecture

| Action        | Location  | Cost         |
| ------------- | --------- | ------------ |
| Create Market | Off-chain | Free         |
| Initialize    | On-chain  | Backend pays |
| Vote          | On-chain  | User pays    |
| Claim         | On-chain  | User pays    |
