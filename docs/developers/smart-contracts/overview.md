# Smart Contracts Overview

Predictly uses **Move smart contracts** deployed on Movement Network to handle all on-chain operations including market creation, voting, resolution, and reward distribution.

## Contract Information

| Property             | Value                                                                                                                                                                          |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Network**          | Movement Testnet (Bardock)                                                                                                                                                     |
| **Contract Address** | `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565`                                                                                                           |
| **Module**           | `predictly::market`                                                                                                                                                            |
| **Language**         | Move                                                                                                                                                                           |
| **Explorer**         | [View on Explorer](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet) |

## Quick Links

- **[View Functions](view-functions.md)** - Read data from the blockchain
- **[Entry Functions](entry-functions.md)** - Write data to the blockchain
- **[TypeScript Integration](typescript-integration.md)** - Integrate with frontend
- **[Deployment Guide](deployment.md)** - Deploy your own instance

## Contract Architecture

The Predictly smart contract is organized into several key components:

### Core Modules

```
predictly/
├── market.move          # Main market logic
├── vote.move            # Voting mechanism
├── reward.move          # Reward calculation
└── types.move           # Shared types and constants
```

### Key Features

✅ **Market Creation** - Create prediction markets with custom parameters  
✅ **Voting System** - YES/NO voting with stake amounts  
✅ **Resolution** - Judge-based market resolution  
✅ **Reward Distribution** - Automatic reward calculation  
✅ **Multiple Market Types** - Standard and No-Loss markets

## Data Structures

### Market

```move
struct Market has key, store {
    id: u64,
    title: String,
    description: String,
    end_time: u64,
    min_stake: u64,
    max_stake: u64,
    resolver: address,
    market_type: u8,
    status: u8,
    outcome: u8,
    yes_pool: u64,
    no_pool: u64,
    participants: vector<address>,
}
```

### Vote

```move
struct Vote has key, store {
    market_id: u64,
    voter: address,
    prediction: u8,
    amount: u64,
    claimed: bool,
}
```

## Constants

### Market Status

| Value | Status    | Description               |
| ----- | --------- | ------------------------- |
| `0`   | ACTIVE    | Market is open for voting |
| `1`   | RESOLVED  | Market has been resolved  |
| `2`   | CANCELLED | Market has been cancelled |

### Market Outcome

| Value | Outcome | Description                |
| ----- | ------- | -------------------------- |
| `0`   | PENDING | Not yet resolved           |
| `1`   | YES     | YES prediction won         |
| `2`   | NO      | NO prediction won          |
| `3`   | INVALID | Market invalid, refund all |

### Prediction

| Value | Prediction | Description  |
| ----- | ---------- | ------------ |
| `1`   | YES        | Vote for YES |
| `2`   | NO         | Vote for NO  |

### Market Type

| Value | Type     | Description                |
| ----- | -------- | -------------------------- |
| `0`   | STANDARD | Full Degen market          |
| `1`   | NO_LOSS  | Zero Loss market with DeFi |

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

### Basis Points to Percentage

| Basis Points | Percentage |
| ------------ | ---------- |
| 2500         | 25%        |
| 5000         | 50%        |
| 7500         | 75%        |
| 10000        | 100%       |

**Formula:** `percentage = basis_points ÷ 100`

## Function Categories

### View Functions (Read-Only)

These functions read data from the blockchain without requiring a transaction:

- `get_market_count` - Total number of markets
- `get_market_status` - Market status (Active/Resolved/Cancelled)
- `get_market_outcome` - Market result (Pending/Yes/No/Invalid)
- `get_market_pools` - YES and NO pool amounts
- `get_percentages` - YES vs NO percentages
- `get_participant_count` - Number of voters
- `get_vote_prediction` - User's prediction
- `get_vote_amount` - User's stake amount
- `calculate_reward` - Potential reward amount

[View all View Functions →](view-functions.md)

### Entry Functions (State-Changing)

These functions modify blockchain state and require gas fees:

- `create_market` - Create a new prediction market
- `place_vote` - Vote on a market with stake
- `resolve` - Resolve market outcome (judge only)
- `claim_reward` - Claim rewards after resolution

[View all Entry Functions →](entry-functions.md)

## Security Features

### Access Control

- **Market Creation** - Anyone can create markets
- **Voting** - Anyone can vote on active markets
- **Resolution** - Only designated resolver can resolve
- **Claiming** - Only winners can claim rewards

### Safety Checks

✅ **Stake Limits** - Enforces min/max stake amounts  
✅ **Deadline Enforcement** - Cannot vote after deadline  
✅ **Double Voting Prevention** - One vote per user per market  
✅ **Double Claiming Prevention** - Cannot claim twice  
✅ **Status Validation** - Validates market status for each action

## Gas Optimization

The contract is optimized for low gas costs:

- **Efficient Storage** - Minimal on-chain storage
- **Batch Operations** - Where possible
- **View Functions** - Free to call
- **Optimized Calculations** - Efficient reward math

## Testing

The contract includes comprehensive tests:

```bash
# Run all tests
aptos move test --package-dir contracts/predictly

# Run specific test
aptos move test --package-dir contracts/predictly --filter test_create_market
```

[Learn more about testing →](../testing/smart-contracts.md)

## Deployment

To deploy your own instance:

```bash
# Compile
aptos move compile --package-dir contracts/predictly

# Test
aptos move test --package-dir contracts/predictly

# Deploy
aptos move publish --package-dir contracts/predictly --named-addresses predictly=default
```

[Full deployment guide →](deployment.md)

## Integration

### Frontend Integration

Use the Aptos TypeScript SDK to interact with the contract:

```typescript
import { Aptos, AptosConfig } from "@aptos-labs/ts-sdk";

const CONTRACT =
  "0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565";

const config = new AptosConfig({
  fullnode: "https://testnet.movementnetwork.xyz/v1",
});

const aptos = new Aptos(config);

// Read market data
const [count] = await aptos.view({
  payload: {
    function: `${CONTRACT}::market::get_market_count`,
    functionArguments: [CONTRACT],
  },
});
```

[Full TypeScript integration guide →](typescript-integration.md)

### Backend Integration

The Predictly backend uses the contract to:

1. Initialize markets on-chain (backend pays gas)
2. Sync market data from blockchain
3. Build transaction payloads for frontend
4. Monitor contract events

[Learn more about backend integration →](../backend-api/hybrid-flow.md)

## Resources

- **[Movement Network Docs](https://docs.movementnetwork.xyz/)** - Movement Network documentation
- **[Move Language Book](https://move-language.github.io/move/)** - Learn Move programming
- **[Aptos SDK Docs](https://aptos.dev/sdks/ts-sdk/)** - TypeScript SDK documentation
- **[Contract Explorer](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet)** - View contract on explorer

## Support

Need help with the smart contracts?

- **[GitHub Issues](https://github.com/your-username/predictly/issues)** - Report bugs
- **[Discord](community/discord.md)** - Ask questions
- **[Documentation](view-functions.md)** - Read the docs

---

**Next Steps:**

- [View Functions →](view-functions.md)
- [Entry Functions →](entry-functions.md)
- [TypeScript Integration →](typescript-integration.md)
