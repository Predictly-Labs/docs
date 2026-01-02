# Entry Functions

Entry functions allow you to write data to the Predictly smart contract. These functions modify blockchain state and require:

- A connected wallet
- Gas fees (paid in MOVE tokens)
- Transaction signing

## Contract Information

```
Network: Movement Testnet (Bardock)
Contract: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
Module: predictly::market
```

## Access Methods

### Via Explorer

1. Open [Contract Module](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet)
2. Click the **"Run"** tab (not "View")
3. Connect your wallet
4. Select the function
5. Fill in parameters
6. Click **"Run"** and approve transaction

### Via CLI

Use the Aptos CLI with your wallet:

```bash
aptos move run \
  --function-id CONTRACT::market::FUNCTION_NAME \
  --args TYPE:VALUE ...
```

### Via TypeScript

See [TypeScript Integration](typescript-integration.md) for frontend integration.

---

## Available Entry Functions

### create_market

Create a new prediction market.

**Parameters:**

| Name          | Type    | Description                      | Example                                                              |
| ------------- | ------- | -------------------------------- | -------------------------------------------------------------------- |
| `admin_addr`  | address | Contract address                 | `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565` |
| `title`       | String  | Market title                     | `"Will BTC reach $100k?"`                                            |
| `description` | String  | Market description               | `"Bitcoin price prediction for 2025"`                                |
| `end_time`    | u64     | Unix timestamp (voting deadline) | `1735689600`                                                         |
| `min_stake`   | u64     | Minimum stake in octas           | `10000000` (0.1 MOVE)                                                |
| `max_stake`   | u64     | Maximum stake (0 = unlimited)    | `1000000000` (10 MOVE)                                               |
| `resolver`    | address | Who can resolve the market       | `0x9161...`                                                          |
| `market_type` | u8      | 0=STANDARD, 1=NO_LOSS            | `0`                                                                  |

**Example (Explorer):**

```
admin_addr: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
title: Will BTC reach $100k in 2025?
description: Bitcoin must reach or exceed $100,000 USD by Dec 31, 2025
end_time: 1735689600
min_stake: 10000000
max_stake: 1000000000
resolver: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
market_type: 0
```

**CLI Example:**

```bash
aptos move run \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::create_market \
  --args \
    address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 \
    string:"Will BTC reach $100k?" \
    string:"Bitcoin price prediction" \
    u64:1735689600 \
    u64:10000000 \
    u64:1000000000 \
    address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 \
    u8:0
```

**Notes:**

- Anyone can create a market
- Gas fees apply
- Market ID is auto-incremented (0, 1, 2, ...)
- Use [Unix Timestamp Converter](https://www.unixtimestamp.com/) for `end_time`

---

### place_vote

Vote on a market by staking MOVE tokens.

**Parameters:**

| Name         | Type    | Description           | Example              |
| ------------ | ------- | --------------------- | -------------------- |
| `admin_addr` | address | Contract address      | `0x9161...`          |
| `market_id`  | u64     | Market ID             | `0`                  |
| `prediction` | u8      | 1=YES, 2=NO           | `1`                  |
| `amount`     | u64     | Stake amount in octas | `100000000` (1 MOVE) |

**Example (Explorer):**

```
admin_addr: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
market_id: 0
prediction: 1
amount: 100000000
```

**CLI Example:**

```bash
aptos move run \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::place_vote \
  --args \
    address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 \
    u64:0 \
    u8:1 \
    u64:100000000
```

**Requirements:**

- ✅ Market must be ACTIVE
- ✅ Must have MOVE tokens in wallet
- ✅ Amount must be >= `min_stake` and <= `max_stake`
- ✅ Voting must be before `end_time`

**Restrictions:**

- ❌ Cannot vote twice on the same market
- ❌ Cannot vote after deadline
- ❌ Cannot vote on resolved/cancelled markets

---

### resolve

Resolve a market and determine the outcome.

**Parameters:**

| Name         | Type    | Description            | Example     |
| ------------ | ------- | ---------------------- | ----------- |
| `admin_addr` | address | Contract address       | `0x9161...` |
| `market_id`  | u64     | Market ID              | `0`         |
| `outcome`    | u8      | 1=YES, 2=NO, 3=INVALID | `1`         |

**Outcome Options:**

| Value | Outcome | Effect                         |
| ----- | ------- | ------------------------------ |
| `1`   | YES     | YES voters win, NO voters lose |
| `2`   | NO      | NO voters win, YES voters lose |
| `3`   | INVALID | Everyone gets refund           |

**Example (Explorer):**

```
admin_addr: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
market_id: 0
outcome: 1
```

**CLI Example:**

```bash
aptos move run \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::resolve \
  --args \
    address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 \
    u64:0 \
    u8:1
```

**Requirements:**

- ✅ Only the designated `resolver` can call this
- ✅ Market must be ACTIVE
- ✅ Current time must be after `end_time`

**Restrictions:**

- ❌ Cannot resolve before deadline
- ❌ Cannot resolve twice
- ❌ Only resolver address can call

**Use INVALID (3) when:**

- Market question is ambiguous
- Outcome cannot be determined
- Market should be cancelled
- Everyone gets their stake back

---

### claim_reward

Claim your reward after a market is resolved.

**Parameters:**

| Name         | Type    | Description      | Example     |
| ------------ | ------- | ---------------- | ----------- |
| `admin_addr` | address | Contract address | `0x9161...` |
| `market_id`  | u64     | Market ID        | `0`         |

**Example (Explorer):**

```
admin_addr: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
market_id: 0
```

**CLI Example:**

```bash
aptos move run \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::claim_reward \
  --args \
    address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 \
    u64:0
```

**Requirements:**

- ✅ Market must be RESOLVED
- ✅ You must have voted on the market
- ✅ You must have voted for the winning outcome (or market is INVALID)

**Restrictions:**

- ❌ Cannot claim before market is resolved
- ❌ Cannot claim if you didn't vote
- ❌ Cannot claim if you voted for losing outcome
- ❌ Cannot claim twice

**Reward Calculation:**

For winning voters:

```
Your Reward = (Your Stake / Total Winning Stake) × Total Losing Stake
Total Received = Your Stake + Your Reward
```

For INVALID outcome:

```
Everyone gets their stake back (refund)
```

**Example:**

- You voted YES with 10 MOVE
- Total YES: 100 MOVE
- Total NO: 50 MOVE
- Market resolved: YES
- Your share: 10 / 100 = 10%
- Your reward: 10% × 50 MOVE = 5 MOVE
- You receive: 10 + 5 = **15 MOVE**

---

## Complete Flow Example

Here's a complete example of the market lifecycle:

```
1. Alice: create_market()
   → Market ID = 0
   → Title: "Will it rain tomorrow?"
   → Deadline: Tomorrow 12:00 PM
   → Resolver: Alice

2. Bob: place_vote(market_id=0, prediction=YES, amount=1 MOVE)
   → Bob stakes 1 MOVE on YES

3. Carol: place_vote(market_id=0, prediction=NO, amount=0.5 MOVE)
   → Carol stakes 0.5 MOVE on NO

4. Dave: place_vote(market_id=0, prediction=YES, amount=2 MOVE)
   → Dave stakes 2 MOVE on YES

   Current state:
   - YES pool: 3 MOVE (Bob + Dave)
   - NO pool: 0.5 MOVE (Carol)
   - Total: 3.5 MOVE

5. ... Time passes, deadline reached ...

6. Alice: resolve(market_id=0, outcome=YES)
   → It rained! YES wins

7. Bob: claim_reward(market_id=0)
   → Bob's share: 1/3 of 0.5 MOVE = 0.167 MOVE
   → Bob receives: 1 + 0.167 = 1.167 MOVE

8. Dave: claim_reward(market_id=0)
   → Dave's share: 2/3 of 0.5 MOVE = 0.333 MOVE
   → Dave receives: 2 + 0.333 = 2.333 MOVE

9. Carol: claim_reward(market_id=0)
   → Carol voted NO (lost)
   → Cannot claim, loses 0.5 MOVE
```

---

## Conversion Reference

### MOVE to Octas

| MOVE | Octas         |
| ---- | ------------- |
| 0.1  | 10,000,000    |
| 0.5  | 50,000,000    |
| 1.0  | 100,000,000   |
| 5.0  | 500,000,000   |
| 10.0 | 1,000,000,000 |

**Formula:** `octas = MOVE × 100,000,000`

### Unix Timestamp

Convert dates to Unix timestamp:

- Use [Unix Timestamp Converter](https://www.unixtimestamp.com/)

**Examples:**

- Jan 1, 2025 00:00 UTC = `1735689600`
- Feb 1, 2025 00:00 UTC = `1738368000`
- Mar 1, 2025 00:00 UTC = `1740787200`

---

## Gas Fees

All entry functions require gas fees:

| Function        | Typical Gas Cost |
| --------------- | ---------------- |
| `create_market` | ~0.001 MOVE      |
| `place_vote`    | ~0.0005 MOVE     |
| `resolve`       | ~0.0005 MOVE     |
| `claim_reward`  | ~0.0005 MOVE     |

**Note:** Actual gas costs may vary based on network congestion.

---

## Error Handling

Common errors and solutions:

| Error                | Cause                     | Solution                      |
| -------------------- | ------------------------- | ----------------------------- |
| `MARKET_NOT_FOUND`   | Invalid market ID         | Check market exists           |
| `MARKET_NOT_ACTIVE`  | Market resolved/cancelled | Cannot vote on closed markets |
| `VOTING_ENDED`       | Past deadline             | Cannot vote after end_time    |
| `ALREADY_VOTED`      | Voted twice               | One vote per market           |
| `INSUFFICIENT_STAKE` | Below min_stake           | Increase stake amount         |
| `EXCESSIVE_STAKE`    | Above max_stake           | Reduce stake amount           |
| `NOT_RESOLVER`       | Wrong address             | Only resolver can resolve     |
| `NOT_RESOLVED`       | Market still active       | Wait for resolution           |
| `ALREADY_CLAIMED`    | Claimed twice             | Can only claim once           |
| `NOT_WINNER`         | Voted for losing side     | Only winners can claim        |

---

## TypeScript Integration

For frontend integration examples, see [TypeScript Integration](typescript-integration.md).

## Next Steps

- **[View Functions →](view-functions.md)** - Learn how to read contract data
- **[TypeScript Integration →](typescript-integration.md)** - Integrate with your app
- **[Contract Overview →](overview.md)** - Back to overview
