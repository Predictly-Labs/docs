# View Functions

View functions allow you to read data from the Predictly smart contract without requiring a transaction or gas fees. These functions are read-only and can be called by anyone.

## Contract Information

```
Network: Movement Testnet (Bardock)
Contract: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
Module: predictly::market
```

## Access Methods

### Via Explorer

1. Open [Movement Explorer](https://explorer.movementnetwork.xyz/?network=bardock+testnet)
2. Navigate to [Contract Module](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet)
3. Click the **"View"** tab
4. Select the function you want to call
5. Fill in the parameters
6. Click **"Run"**

### Via CLI

Use the Aptos CLI to call view functions:

```bash
aptos move view \
  --function-id CONTRACT::market::FUNCTION_NAME \
  --args TYPE:VALUE ...
```

### Via TypeScript

See [TypeScript Integration](typescript-integration.md) for code examples.

---

## Available View Functions

### get_market_count

Get the total number of markets created.

**Parameters:**

| Name         | Type    | Description      | Example                                                              |
| ------------ | ------- | ---------------- | -------------------------------------------------------------------- |
| `admin_addr` | address | Contract address | `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565` |

**Returns:** `u64` - Total market count

**Example Output:** `["3"]` → 3 markets exist

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_market_count \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
```

---

### get_market_status

Get the current status of a market.

**Parameters:**

| Name         | Type    | Description      | Example    |
| ------------ | ------- | ---------------- | ---------- |
| `admin_addr` | address | Contract address | `0x916...` |
| `market_id`  | u64     | Market ID        | `0`        |

**Returns:** `u8` - Status code

| Value | Status    | Description               |
| ----- | --------- | ------------------------- |
| `0`   | ACTIVE    | Market is open for voting |
| `1`   | RESOLVED  | Market has been resolved  |
| `2`   | CANCELLED | Market has been cancelled |

**Example Output:** `["0"]` → Market is ACTIVE

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_market_status \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0
```

---

### get_market_outcome

Get the resolution outcome of a market.

**Parameters:**

| Name         | Type    | Description      | Example    |
| ------------ | ------- | ---------------- | ---------- |
| `admin_addr` | address | Contract address | `0x916...` |
| `market_id`  | u64     | Market ID        | `0`        |

**Returns:** `u8` - Outcome code

| Value | Outcome | Description                |
| ----- | ------- | -------------------------- |
| `0`   | PENDING | Not yet resolved           |
| `1`   | YES     | YES prediction won         |
| `2`   | NO      | NO prediction won          |
| `3`   | INVALID | Market invalid, refund all |

**Example Output:** `["1"]` → YES won

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_market_outcome \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0
```

---

### get_market_pools

Get the total MOVE staked in YES and NO pools.

**Parameters:**

| Name         | Type    | Description      | Example    |
| ------------ | ------- | ---------------- | ---------- |
| `admin_addr` | address | Contract address | `0x916...` |
| `market_id`  | u64     | Market ID        | `0`        |

**Returns:** `(u64, u64)` - (YES pool, NO pool) in octas

**Example Output:** `["100000000", "50000000"]`

- YES pool: 100,000,000 octas = 1 MOVE
- NO pool: 50,000,000 octas = 0.5 MOVE

**Conversion:** 1 MOVE = 100,000,000 octas

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_market_pools \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0
```

---

### get_percentages

Get the percentage distribution of YES vs NO votes.

**Parameters:**

| Name         | Type    | Description      | Example    |
| ------------ | ------- | ---------------- | ---------- |
| `admin_addr` | address | Contract address | `0x916...` |
| `market_id`  | u64     | Market ID        | `0`        |

**Returns:** `(u64, u64)` - (YES %, NO %) in basis points

**Example Output:** `["6667", "3333"]`

- YES: 6667 basis points = 66.67%
- NO: 3333 basis points = 33.33%

**Conversion:** Divide by 100 to get percentage (5000 = 50%)

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_percentages \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0
```

---

### get_participant_count

Get the number of unique voters in a market.

**Parameters:**

| Name         | Type    | Description      | Example    |
| ------------ | ------- | ---------------- | ---------- |
| `admin_addr` | address | Contract address | `0x916...` |
| `market_id`  | u64     | Market ID        | `0`        |

**Returns:** `u64` - Number of participants

**Example Output:** `["5"]` → 5 people have voted

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_participant_count \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0
```

---

### get_vote_prediction

Get a user's prediction for a specific market.

**Parameters:**

| Name         | Type    | Description            | Example    |
| ------------ | ------- | ---------------------- | ---------- |
| `admin_addr` | address | Contract address       | `0x916...` |
| `market_id`  | u64     | Market ID              | `0`        |
| `voter`      | address | Voter's wallet address | `0xabc...` |

**Returns:** `u8` - Prediction code

| Value | Prediction | Description   |
| ----- | ---------- | ------------- |
| `1`   | YES        | Voted for YES |
| `2`   | NO         | Voted for NO  |

**Example Output:** `["1"]` → Voted YES

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_vote_prediction \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0 address:0xVOTER_ADDRESS
```

---

### get_vote_amount

Get the amount a user staked on a market.

**Parameters:**

| Name         | Type    | Description            | Example    |
| ------------ | ------- | ---------------------- | ---------- |
| `admin_addr` | address | Contract address       | `0x916...` |
| `market_id`  | u64     | Market ID              | `0`        |
| `voter`      | address | Voter's wallet address | `0xabc...` |

**Returns:** `u64` - Stake amount in octas

**Example Output:** `["100000000"]` → Staked 1 MOVE

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_vote_amount \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0 address:0xVOTER_ADDRESS
```

---

### calculate_reward

Calculate the reward a user will receive after market resolution.

**Parameters:**

| Name         | Type    | Description            | Example    |
| ------------ | ------- | ---------------------- | ---------- |
| `admin_addr` | address | Contract address       | `0x916...` |
| `market_id`  | u64     | Market ID              | `0`        |
| `voter`      | address | Voter's wallet address | `0xabc...` |

**Returns:** `u64` - Reward amount in octas

**Example Output:** `["200000000"]` → Will receive 2 MOVE

**Note:** Only works after market is resolved. Returns 0 if user didn't win.

**CLI Example:**

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::calculate_reward \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 u64:0 address:0xVOTER_ADDRESS
```

---

## Quick Reference

### Constants

| Constant        | Values                            |
| --------------- | --------------------------------- |
| **Status**      | 0=ACTIVE, 1=RESOLVED, 2=CANCELLED |
| **Outcome**     | 0=PENDING, 1=YES, 2=NO, 3=INVALID |
| **Prediction**  | 1=YES, 2=NO                       |
| **Market Type** | 0=STANDARD, 1=NO_LOSS             |

### Conversions

| From         | To           | Formula       |
| ------------ | ------------ | ------------- |
| Octas        | MOVE         | ÷ 100,000,000 |
| MOVE         | Octas        | × 100,000,000 |
| Basis Points | Percentage   | ÷ 100         |
| Percentage   | Basis Points | × 100         |

### Common Values

| MOVE | Octas         |
| ---- | ------------- |
| 0.1  | 10,000,000    |
| 0.5  | 50,000,000    |
| 1.0  | 100,000,000   |
| 5.0  | 500,000,000   |
| 10.0 | 1,000,000,000 |

---

## TypeScript Examples

For TypeScript integration examples, see [TypeScript Integration](typescript-integration.md).

## Next Steps

- **[Entry Functions →](entry-functions.md)** - Learn how to write data to the contract
- **[TypeScript Integration →](typescript-integration.md)** - Integrate with your frontend
- **[Contract Overview →](overview.md)** - Back to overview
