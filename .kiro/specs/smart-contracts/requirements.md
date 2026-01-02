# Requirements Document

## Introduction

This document specifies the requirements for Predictly smart contracts on Movement Network. The contracts will handle on-chain prediction markets, staking, voting, and reward distribution. Movement Network uses Move language (Aptos-compatible), so contracts will be written in Move.

## Priority Levels

| Priority | Description | Timeline |
|----------|-------------|----------|
| **P0** | MUST HAVE - Core functionality for demo | Week 1 |
| **P1** | SHOULD HAVE - Important but can be simplified | Week 2 |
| **P2** | NICE TO HAVE - Post-hackathon or if time permits | Post-MVP |

## Glossary

- **Movement Network**: L2 blockchain built on Aptos Move VM
- **Move**: Smart contract language used by Aptos/Movement
- **Prediction Market**: On-chain market where users stake tokens on YES/NO outcomes
- **Stake**: Amount of MOVE tokens locked in a prediction
- **Pool**: Total staked amount (yesPool + noPool)
- **Resolution**: Final outcome determination by authorized resolver
- **Reward**: Tokens distributed to winning voters after resolution
- **No-Loss Market**: Market type where losers get principal back, winners get yield only

---

## P0 Requirements (MVP Core)

### Requirement 1: Market Contract [P0]

**User Story:** As a user, I want to interact with prediction markets on-chain, so that my stakes and votes are trustlessly managed.

**MVP Scope:** Single module approach (no factory pattern for MVP)

#### Acceptance Criteria

1. WHEN a market is created THEN the System SHALL store title, description, endTime, and resolver address
2. WHEN a market is created THEN the System SHALL set status to ACTIVE and initialize pools to zero
3. WHEN querying market state THEN the System SHALL return current pools, percentages, participant count, and status
4. WHEN market endTime passes THEN the System SHALL prevent new votes until resolution

### Requirement 2: Voting/Staking [P0]

**User Story:** As a user, I want to stake MOVE tokens on YES or NO predictions, so that I can participate in the market.

#### Acceptance Criteria

1. WHEN a user places a vote THEN the System SHALL transfer stake amount from user to contract
2. WHEN a user places a vote THEN the System SHALL update the corresponding pool (yesPool or noPool)
3. WHEN a user places a vote THEN the System SHALL record vote details (voter, prediction, amount, timestamp)
4. WHEN a user attempts to vote twice THEN the System SHALL reject the transaction
5. WHEN a vote is placed THEN the System SHALL emit VotePlaced event

### Requirement 3: Market Resolution [P0]

**User Story:** As a resolver, I want to resolve markets after deadline, so that rewards can be distributed.

#### Acceptance Criteria

1. WHEN resolver calls resolve function THEN the System SHALL set outcome (YES/NO/INVALID) and status to RESOLVED
2. WHEN non-resolver attempts to resolve THEN the System SHALL reject the transaction
3. WHEN resolver attempts to resolve before endTime THEN the System SHALL reject the transaction
4. WHEN market is resolved THEN the System SHALL emit MarketResolved event

### Requirement 4: Reward Claims [P0]

**User Story:** As a winning voter, I want to claim my rewards on-chain, so that I receive my earnings trustlessly.

**MVP Scope:** STANDARD market type only (winner takes proportional share of total pool)

#### Acceptance Criteria

1. WHEN a winning voter claims THEN the System SHALL calculate and transfer reward amount
2. WHEN a voter claims THEN the System SHALL mark their vote as claimed to prevent double claims
3. WHEN a losing voter attempts to claim THEN the System SHALL reject (unless INVALID outcome)
4. WHEN claiming from INVALID market THEN the System SHALL return original stake amount
5. WHEN a voter claims THEN the System SHALL emit RewardClaimed event

**Reward Calculation (STANDARD):**
```
winner_reward = (voter_stake / winning_pool) * total_pool
```

---

## P1 Requirements (Week 2 / If Time Permits)

### Requirement 5: Mock DeFi Protocols [P1]

**User Story:** As a platform, I want to integrate with DeFi lending protocols, so that staked funds generate yield for no-loss markets.

**MVP Scope:** Create mock contracts simulating LayerBank, Canopy, and MovePosition. These demonstrate the architecture - in production would connect to real protocols.

#### Supported Protocols (Mock)

| Protocol | Type | Mock APY |
|----------|------|----------|
| LayerBank | Lending | 5% |
| Canopy | Lending | 4% |
| MovePosition | Lending | 6% |

#### Acceptance Criteria

1. WHEN funds are deposited to mock protocol THEN the System SHALL record deposit amount and timestamp
2. WHEN funds are withdrawn THEN the System SHALL return principal + accrued yield
3. WHEN querying balance THEN the System SHALL return current principal + pending yield
4. WHEN calculating yield THEN the System SHALL use protocol's mock APY rate (prorated by time)

**Mock Protocol Interface:**
```move
module predictly::mock_lending {
    struct LendingPool has key {
        name: String,              // "LayerBank", "Canopy", "MovePosition"
        apy_bps: u64,              // APY in basis points (500 = 5%)
        total_deposits: Coin<AptosCoin>,
        deposits: SimpleMap<address, DepositInfo>,
    }

    struct DepositInfo has store, drop {
        amount: u64,
        timestamp: u64,
    }

    public entry fun deposit(user: &signer, amount: u64);
    public entry fun withdraw(user: &signer): (u64, u64); // (principal, yield)

    #[view]
    public fun get_balance(user: address): u64;
    #[view]
    public fun get_pending_yield(user: address): u64;
}
```

**Yield Calculation:**
```
seconds_staked = current_time - deposit_timestamp
yield = principal * (apy_bps / 10000) * (seconds_staked / 31536000)
```

### Requirement 6: No-Loss Market Type [P1]

**User Story:** As a user, I want to participate in no-loss markets, so that I never lose my principal.

**Integration:** No-loss markets route funds to Mock DeFi Protocols for yield generation.

#### Architecture Flow

```
User stakes 100 MOVE (NO_LOSS market)
         │
         ▼
┌─────────────────┐     deposit      ┌──────────────────┐
│ PredictionMarket│ ───────────────▶ │  MockLendingPool │
│                 │                  │  (LayerBank)     │
│  yes_pool: 100  │                  │                  │
│  no_pool: 50    │                  │  deposits: 150   │
└─────────────────┘                  │  yield: ~0.02    │
         │                           └──────────────────┘
         │ resolve (YES wins)
         ▼
┌─────────────────┐     withdraw     ┌──────────────────┐
│ Winner claims   │ ◀─────────────── │  Returns:        │
│                 │                  │  - 150 principal │
│  Gets: stake +  │                  │  - 0.02 yield    │
│  proportional   │                  └──────────────────┘
│  yield          │
└─────────────────┘
```

#### Acceptance Criteria

1. WHEN a NO_LOSS market is created THEN the System SHALL flag it as principal-protected (marketType = 1)
2. WHEN a user votes in NO_LOSS market THEN the System SHALL deposit stake to selected lending protocol
3. WHEN a NO_LOSS market is resolved THEN the System SHALL withdraw all funds + yield from lending protocol
4. WHEN a NO_LOSS market is resolved THEN the System SHALL return principal to ALL voters (winners and losers)
5. WHEN a NO_LOSS market is resolved THEN the System SHALL distribute yield rewards to winners only (proportional to stake)

**Reward Calculation (NO_LOSS):**
```
// After resolution, withdraw from lending pool
total_principal = yes_pool + no_pool
total_yield = lending_pool.withdraw() - total_principal

// Everyone gets principal back
principal_return = voter_stake

// Winners split the yield proportionally
IF voter is winner:
    winner_yield = (voter_stake / winning_pool) * total_yield
    total_reward = principal_return + winner_yield
ELSE:
    total_reward = principal_return  // Losers get principal back, no yield
```

### Requirement 7: Stake Limits [P1]

**User Story:** As a platform, I want to enforce stake limits to prevent market manipulation.

#### Acceptance Criteria

1. WHEN stake amount is below minStake THEN the System SHALL reject the transaction
2. WHEN stake amount exceeds maxStake THEN the System SHALL reject the transaction

### Requirement 8: Basic Events [P1]

**User Story:** As a backend service, I want to listen to contract events, so that I can sync on-chain data.

#### Acceptance Criteria

1. WHEN MarketCreated event is emitted THEN the event SHALL include marketId, creator, title, endTime
2. WHEN VotePlaced event is emitted THEN the event SHALL include marketId, voter, prediction, amount
3. WHEN MarketResolved event is emitted THEN the event SHALL include marketId, outcome, resolver
4. WHEN RewardClaimed event is emitted THEN the event SHALL include marketId, voter, amount

---

## P2 Requirements (Post-Hackathon)

### Requirement 9: Market Factory [P2]

**User Story:** As a platform admin, I want to deploy and manage prediction market contracts, so that users can create markets on-chain.

#### Acceptance Criteria

1. WHEN the factory contract is initialized THEN the System SHALL set the admin address and treasury address
2. WHEN an authorized user creates a market THEN the System SHALL deploy a new market instance with unique ID
3. WHEN a market is created THEN the System SHALL emit MarketCreated event with market ID and parameters
4. WHEN querying markets THEN the System SHALL return list of all market addresses

### Requirement 10: Admin Functions [P2]

**User Story:** As an admin, I want to manage platform parameters, so that I can maintain the system.

#### Acceptance Criteria

1. WHEN admin updates treasury address THEN the System SHALL store new treasury for fee collection
2. WHEN admin pauses a market THEN the System SHALL prevent new votes until unpaused
3. WHEN admin sets platform fee THEN the System SHALL deduct fee percentage from reward distributions
4. WHEN querying admin functions THEN the System SHALL verify caller is admin

### Requirement 11: Real DeFi Integration [P2]

**User Story:** As a platform, I want to replace mock protocols with real DeFi integrations for production yield.

**Note:** This replaces the Mock DeFi Protocols (Requirement 5) with real protocol integrations.

#### Acceptance Criteria

1. WHEN stakes are deposited THEN the System SHALL route funds to REAL DeFi protocols (Canopy, LayerBank, MovePosition)
2. WHEN market is resolved THEN the System SHALL withdraw funds + actual yield from DeFi protocols
3. WHEN calculating rewards THEN the System SHALL use actual yield earned (not mock APY)

---

## MVP Demo Flow

```
1. Admin deploys market contract with parameters
2. User A connects wallet, votes YES with 10 MOVE
3. User B connects wallet, votes NO with 5 MOVE
4. After endTime, resolver resolves market as YES
5. User A claims reward (gets ~15 MOVE total)
6. User B cannot claim (lost the bet)
```

## Technical Notes

- **Blockchain**: Movement Network (Aptos-compatible)
- **Language**: Move
- **Token**: Native MOVE (AptosCoin equivalent)
- **CLI**: aptos-cli (compatible with Movement)
- **Testing**: Movement Testnet
