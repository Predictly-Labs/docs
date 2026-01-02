# Design Document

## Overview

Predictly smart contracts for Movement Network (Aptos Move VM). The MVP uses a single-module approach for simplicity, with factory pattern deferred to post-hackathon (P2).

## Priority-Based Architecture

### P0 (MVP Core) - Single Module Approach
```
┌─────────────────────────────────────────────────────────────┐
│                    Movement Network                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              PredictionMarket Module                  │   │
│  │                                                       │   │
│  │  - create_market (admin creates directly)            │   │
│  │  - place_vote                                        │   │
│  │  - resolve                                           │   │
│  │  - claim_reward (STANDARD type only)                 │   │
│  │  - get_state / view functions                        │   │
│  └──────────────────────────────────────────────────────┘   │
│           │                                                  │
│           ▼                                                  │
│  ┌──────────────────┐                                       │
│  │   MOVE Token      │◀── Staking/Rewards                   │
│  └──────────────────┘                                       │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Design Decision:** MVP uses single module without factory pattern to reduce complexity and accelerate demo timeline. Markets are created directly by admin.

### P1 (Week 2) - Add No-Loss + Mock DeFi
```
┌─────────────────────────────────────────────────────────────┐
│                    Movement Network                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              PredictionMarket Module                  │   │
│  │  (+ NO_LOSS market type support)                     │   │
│  └──────────────────────────────────────────────────────┘   │
│           │                                                  │
│           │ deposit/withdraw (NO_LOSS markets only)         │
│           ▼                                                  │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              MockLending Module                       │   │
│  │                                                       │   │
│  │  Pools: LayerBank (5%), Canopy (4%), MovePosition(6%)│   │
│  │  - deposit / withdraw                                │   │
│  │  - get_balance / get_pending_yield                   │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Design Decision:** Mock DeFi protocols simulate yield generation for no-loss markets. This demonstrates the architecture without requiring real protocol integrations during hackathon.

### P2 (Post-Hackathon) - Factory Pattern + Real DeFi
```
┌─────────────────────────────────────────────────────────────┐
│                    Movement Network                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────┐     ┌──────────────────────────────┐  │
│  │  PredictlyFactory │────▶│  PredictionMarket (per market)│  │
│  │                   │     └──────────────────────────────┘  │
│  │  - create_market  │                    │                  │
│  │  - get_markets    │                    ▼                  │
│  │  - admin_funcs    │     ┌──────────────────────────────┐  │
│  └──────────────────┘     │  Real DeFi Protocols          │  │
│                            │  (Canopy, LayerBank, etc.)    │  │
│                            └──────────────────────────────┘  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

## Components and Interfaces

### 1. PredictionMarket Module [P0 Core]

**Design Decision:** Single module handles market creation, voting, resolution, and claims. No factory pattern for MVP - markets stored in a registry within the module itself.

```move
module predictly::market {
    // Constants
    const STATUS_ACTIVE: u8 = 0;
    const STATUS_RESOLVED: u8 = 1;
    const STATUS_CANCELLED: u8 = 2;

    const OUTCOME_PENDING: u8 = 0;
    const OUTCOME_YES: u8 = 1;
    const OUTCOME_NO: u8 = 2;
    const OUTCOME_INVALID: u8 = 3;

    const PREDICTION_YES: u8 = 1;
    const PREDICTION_NO: u8 = 2;

    const MARKET_TYPE_STANDARD: u8 = 0;  // P0: Winner takes pool
    const MARKET_TYPE_NO_LOSS: u8 = 1;   // P1: Principal protected

    // Structs
    struct MarketConfig has key {
        admin: address,
        market_count: u64,
    }

    struct MarketRegistry has key {
        markets: vector<address>,
    }

    struct Market has key {
        id: u64,
        creator: address,
        title: String,
        description: String,
        end_time: u64,
        min_stake: u64,        // P1: Stake limits
        max_stake: u64,        // P1: Stake limits
        market_type: u8,       // P1: NO_LOSS support
        resolver: address,
        status: u8,
        outcome: u8,
        yes_pool: u64,
        no_pool: u64,
        participant_count: u64,
        created_at: u64,
        resolved_at: u64,
    }

    struct Vote has store, drop, copy {
        voter: address,
        prediction: u8,
        amount: u64,
        timestamp: u64,
        has_claimed: bool,
    }

    struct VoteRegistry has key {
        votes: vector<Vote>,
        voter_map: SimpleMap<address, u64>,  // voter -> vote index
    }

    struct MarketVault has key {
        coins: Coin<AptosCoin>,
    }

    // Entry Functions [P0]
    public entry fun initialize(admin: &signer);
    
    public entry fun create_market(
        admin: &signer,
        title: String,
        description: String,
        end_time: u64,
        resolver: address,
    );

    public entry fun place_vote(
        voter: &signer,
        market_id: u64,
        prediction: u8,
        amount: u64,
    );

    public entry fun resolve(
        resolver: &signer,
        market_id: u64,
        outcome: u8,
    );

    public entry fun claim_reward(
        voter: &signer,
        market_id: u64,
    );

    // View Functions [P0]
    #[view]
    public fun get_market_state(market_id: u64): Market;
    #[view]
    public fun get_vote(market_id: u64, voter: address): Vote;
    #[view]
    public fun get_all_votes(market_id: u64): vector<Vote>;
    #[view]
    public fun calculate_reward(market_id: u64, voter: address): u64;
    #[view]
    public fun get_percentages(market_id: u64): (u64, u64);
}
```

### 2. MockLending Module [P1]

**Design Decision:** Mock protocols simulate DeFi yield for no-loss markets. Uses simple time-based APY calculation. In production (P2), this would be replaced with real protocol integrations.

```move
module predictly::mock_lending {
    // Supported mock protocols
    const PROTOCOL_LAYERBANK: u8 = 0;   // 5% APY
    const PROTOCOL_CANOPY: u8 = 1;      // 4% APY
    const PROTOCOL_MOVEPOSITION: u8 = 2; // 6% APY

    struct LendingPool has key {
        protocol_id: u8,
        name: String,
        apy_bps: u64,              // APY in basis points (500 = 5%)
        total_deposits: Coin<AptosCoin>,
    }

    struct DepositInfo has store, drop, copy {
        depositor: address,
        amount: u64,
        timestamp: u64,
    }

    struct DepositRegistry has key {
        deposits: SimpleMap<address, DepositInfo>,
    }

    // Entry Functions
    public entry fun initialize_pool(
        admin: &signer,
        protocol_id: u8,
        name: String,
        apy_bps: u64,
    );

    public entry fun deposit(
        depositor: &signer,
        protocol_id: u8,
        amount: u64,
    );

    public entry fun withdraw(
        depositor: &signer,
        protocol_id: u8,
    );

    // View Functions
    #[view]
    public fun get_balance(protocol_id: u8, depositor: address): u64;
    #[view]
    public fun get_pending_yield(protocol_id: u8, depositor: address): u64;
    #[view]
    public fun get_total_with_yield(protocol_id: u8, depositor: address): (u64, u64); // (principal, yield)
}
```

**Yield Calculation:**
```
seconds_staked = current_time - deposit_timestamp
yield = principal * (apy_bps / 10000) * (seconds_staked / 31536000)
```

### 3. PredictlyFactory Module [P2 - Post-Hackathon]

**Design Decision:** Factory pattern deferred to P2. Enables permissionless market creation and better scalability for production.

```move
module predictly::factory {
    struct FactoryConfig has key {
        admin: address,
        treasury: address,
        platform_fee_bps: u64,  // basis points (100 = 1%)
        market_count: u64,
        paused: bool,
    }

    struct MarketRegistry has key {
        markets: vector<address>,
    }

    // Entry Functions
    public entry fun initialize(admin: &signer, treasury: address);
    public entry fun create_market(
        creator: &signer,
        title: String,
        description: String,
        end_time: u64,
        min_stake: u64,
        max_stake: u64,
        market_type: u8,
        resolver: address,
    );
    public entry fun update_treasury(admin: &signer, new_treasury: address);
    public entry fun update_fee(admin: &signer, new_fee_bps: u64);
    public entry fun pause(admin: &signer);
    public entry fun unpause(admin: &signer);

    // View Functions
    #[view]
    public fun get_market_count(): u64;
    #[view]
    public fun get_markets(): vector<address>;
    #[view]
    public fun get_config(): FactoryConfig;
}
```

### 4. Events Module [P1]

**Design Decision:** Events are P1 priority. MVP can function without events, but they're needed for backend sync and indexing.

```move
module predictly::events {
    // Req 8.1, 8.2: MarketCreated event
    struct MarketCreated has drop, store {
        market_id: u64,
        creator: address,
        title: String,
        end_time: u64,
        market_type: u8,
    }

    // Req 8.3: VotePlaced event
    struct VotePlaced has drop, store {
        market_id: u64,
        voter: address,
        prediction: u8,
        amount: u64,
        new_yes_pool: u64,
        new_no_pool: u64,
    }

    // Req 8.4: MarketResolved event
    struct MarketResolved has drop, store {
        market_id: u64,
        outcome: u8,
        resolver: address,
        total_pool: u64,
    }

    // Req 8.5: RewardClaimed event
    struct RewardClaimed has drop, store {
        market_id: u64,
        voter: address,
        amount: u64,
    }
}
```

## Data Models

### Market States
```
ACTIVE (0) ──▶ RESOLVED (1)
    │              │
    └──────────────┴──▶ CANCELLED (2)
```

### Reward Calculation

**Standard Market [P0]:**
```
// Req 4.1: Winner takes proportional share of total pool
winner_reward = (voter_stake / winning_pool) * total_pool

// Note: Platform fee deferred to P2 (Admin Functions)
```

**No-Loss Market [P1]:**
```
// Req 6.4: Everyone gets principal back
principal_return = voter_stake

// Req 6.5: Winners split yield from mock lending protocol
// Yield calculated by MockLending module based on protocol APY
total_yield = mock_lending::get_pending_yield(protocol_id, market_address)

IF voter is winner:
    winner_yield = (voter_stake / winning_pool) * total_yield
    total_reward = principal_return + winner_yield
ELSE:
    total_reward = principal_return  // Losers get principal back only
```

### No-Loss Market Flow [P1]

```
User stakes 100 MOVE in NO_LOSS market
         │
         ▼
┌─────────────────┐     deposit()     ┌──────────────────┐
│ PredictionMarket│ ────────────────▶ │  MockLending     │
│                 │                   │  (LayerBank 5%)  │
│  yes_pool: 100  │                   │                  │
│  no_pool: 50    │                   │  deposits: 150   │
└─────────────────┘                   └──────────────────┘
         │
         │ resolve(YES wins)
         ▼
┌─────────────────┐     withdraw()    ┌──────────────────┐
│ Market resolved │ ◀──────────────── │  Returns:        │
│                 │                   │  - 150 principal │
│ YES voters get: │                   │  - yield amount  │
│  stake + yield  │                   └──────────────────┘
│                 │
│ NO voters get:  │
│  stake only     │
└─────────────────┘
```

## Error Handling

### Error Codes
```move
// P0 Core Errors
const E_NOT_ADMIN: u64 = 1;
const E_NOT_RESOLVER: u64 = 2;           // Req 3.2
const E_MARKET_NOT_ACTIVE: u64 = 3;      // Req 1.4
const E_MARKET_NOT_ENDED: u64 = 4;       // Req 3.3
const E_ALREADY_VOTED: u64 = 5;          // Req 2.4
const E_NOT_WINNER: u64 = 8;             // Req 4.3
const E_ALREADY_CLAIMED: u64 = 9;        // Req 4.2
const E_INVALID_PREDICTION: u64 = 11;
const E_INVALID_OUTCOME: u64 = 12;

// P1 Stake Limit Errors
const E_STAKE_TOO_LOW: u64 = 6;          // Req 7.1
const E_STAKE_TOO_HIGH: u64 = 7;         // Req 7.2

// P2 Admin Errors
const E_MARKET_PAUSED: u64 = 10;         // Req 10.2
```

## Testing Strategy

### P0 Unit Tests (Move)
- Test market creation with valid/invalid params (Req 1.1, 1.2)
- Test voting mechanics and pool updates (Req 2.1, 2.2, 2.3)
- Test double-vote prevention (Req 2.4)
- Test resolution by resolver vs non-resolver (Req 3.1, 3.2)
- Test resolution timing (Req 3.3)
- Test reward calculations for STANDARD market (Req 4.1)
- Test claim mechanics and double-claim prevention (Req 4.2)
- Test losing voter claim rejection (Req 4.3)
- Test INVALID outcome refund (Req 4.4)

### P0 Integration Tests
- End-to-end flow: create → vote → resolve → claim
- Multiple voters scenario
- Edge cases: single voter, all YES/NO votes

### P1 Unit Tests
- Test stake limits (Req 7.1, 7.2)
- Test mock lending deposit/withdraw
- Test yield calculation accuracy
- Test no-loss principal protection (Req 6.4)
- Test no-loss yield distribution (Req 6.5)

### P1 Integration Tests
- No-loss market full cycle with mock lending
- Event emission verification (Req 8.1-8.5)

## Security Considerations

1. **Reentrancy**: Move's resource model prevents reentrancy by design
2. **Access Control**: Resolver-only resolution (Req 3.2), admin-only market creation for MVP
3. **Overflow**: Use checked math operations for pool calculations
4. **Time Manipulation**: Use block timestamp, accept minor variance for endTime checks
5. **Double Claims**: Vote.has_claimed flag prevents multiple claims (Req 4.2)
6. **Double Voting**: voter_map prevents duplicate votes (Req 2.4)
7. **Front-running**: Consider commit-reveal for large stakes (P2 enhancement)

## Design Decisions Summary

| Decision | Rationale | Priority |
|----------|-----------|----------|
| Single module (no factory) | Faster MVP development, simpler deployment | P0 |
| Admin-only market creation | Security for MVP, permissionless deferred to P2 | P0 |
| STANDARD market type only | Core functionality first, no-loss adds complexity | P0 |
| Mock DeFi protocols | Demonstrates architecture without real integrations | P1 |
| Events as separate module | Clean separation, easier to extend | P1 |
| Factory pattern | Scalability for production, permissionless creation | P2 |
| Platform fees | Revenue model, requires treasury management | P2 |
