# Implementation Plan

## Phase 1: Project Setup

- [x] 1. Setup Move Development Environment


  - [x] 1.1 Initialize Move project structure


    - Create `contracts/predictly/` directory
    - Setup Move.toml with Movement Network dependencies
    - Configure for Movement devnet addresses
    - _Requirements: 1.1_
  - [x] 1.2 Create test account configuration


    - Create `.env` file for contract deployment keys
    - Document test account setup for devnet
    - _Requirements: 1.1_

## Phase 2: Core Market Contract [P0]

- [x] 2. Implement PredictionMarket Module

  - [x] 2.1 Create market module structure and constants

    - Define STATUS_ACTIVE, STATUS_RESOLVED, STATUS_CANCELLED constants
    - Define OUTCOME_PENDING, OUTCOME_YES, OUTCOME_NO, OUTCOME_INVALID constants
    - Define PREDICTION_YES, PREDICTION_NO constants
    - Define MARKET_TYPE_STANDARD constant
    - Define error codes (E_NOT_ADMIN, E_NOT_RESOLVER, etc.)
    - _Requirements: 1.1, 1.2_

  - [x] 2.2 Implement core data structures
    - Create MarketConfig struct with admin and market_count
    - Create Market struct with all fields (id, creator, title, description, end_time, resolver, status, outcome, pools, etc.)
    - Create Vote struct (voter, prediction, amount, timestamp, has_claimed)
    - Create VoteRegistry struct with votes vector and voter_map
    - Create MarketVault struct for holding staked coins
    - _Requirements: 1.1, 1.2, 2.1, 2.2, 2.3_
  - [x] 2.3 Implement initialize and create_market functions

    - Initialize function sets admin and market_count
    - create_market stores title, description, endTime, resolver
    - Set status to ACTIVE and initialize pools to zero
    - _Requirements: 1.1, 1.2_

  - [x] 2.4 Implement place_vote function
    - Validate market is ACTIVE and not ended
    - Transfer stake amount from user to vault
    - Update corresponding pool (yesPool or noPool)
    - Record vote details (voter, prediction, amount, timestamp)
    - Prevent double voting via voter_map
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.5 Implement resolve function
    - Verify caller is resolver (reject non-resolver)
    - Verify market has ended (reject if before endTime)
    - Set outcome (YES/NO/INVALID) and status to RESOLVED
    - _Requirements: 3.1, 3.2, 3.3_

  - [x] 2.6 Implement claim_reward function (STANDARD type)
    - Calculate reward: (voter_stake / winning_pool) * total_pool
    - Verify voter is winner (or INVALID outcome for refund)
    - Mark vote as claimed to prevent double claims
    - Transfer reward from vault to voter
    - Handle INVALID outcome: return original stake
    - _Requirements: 4.1, 4.2, 4.3, 4.4_

  - [x] 2.7 Implement view functions
    - get_market_state: return current pools, percentages, participant count, status
    - get_vote: return vote details for a voter
    - get_all_votes: return all votes for a market
    - calculate_reward: calculate potential reward for a voter
    - get_percentages: return YES/NO percentages
    - _Requirements: 1.3_

- [x] 3. Checkpoint - Core Contract Compilation

  - Compile contracts with `movement move compile`
  - Fix any compilation errors
  - Ensure all tests pass, ask the user if questions arise

## Phase 3: Events Module [P1]

- [x] 4. Implement Events
  - [x] 4.1 Create events module with event structs
    - MarketCreated: market_id, creator, title, end_time, market_type
    - VotePlaced: market_id, voter, prediction, amount, new_yes_pool, new_no_pool
    - MarketResolved: market_id, outcome, resolver, total_pool
    - RewardClaimed: market_id, voter, amount
    - _Requirements: 8.1, 8.2, 8.3, 8.4_
  - [x] 4.2 Integrate events in market module
    - Emit MarketCreated on create_market
    - Emit VotePlaced on place_vote
    - Emit MarketResolved on resolve
    - Emit RewardClaimed on claim_reward
    - _Requirements: 2.5, 3.4, 4.5, 8.1, 8.2, 8.3, 8.4_

## Phase 4: Stake Limits [P1]

- [x] 5. Implement Stake Limits
  - [x] 5.1 Add min_stake and max_stake to Market struct
    - Update create_market to accept stake limit parameters
    - _Requirements: 7.1, 7.2_
  - [x] 5.2 Add stake validation in place_vote
    - Reject if stake amount below minStake
    - Reject if stake amount exceeds maxStake
    - _Requirements: 7.1, 7.2_

## Phase 5: Mock DeFi & No-Loss Markets [P1]

- [x] 6. Implement MockLending Module



  - [x] 6.1 Create mock lending module structure

    - Define PROTOCOL_LAYERBANK (5% APY), PROTOCOL_CANOPY (4% APY), PROTOCOL_MOVEPOSITION (6% APY)
    - Create LendingPool struct (protocol_id, name, apy_bps, total_deposits)
    - Create DepositInfo struct (depositor, amount, timestamp)
    - Create DepositRegistry struct
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [x] 6.2 Implement mock lending functions
    - initialize_pool: setup pool with protocol_id, name, apy_bps
    - deposit: record deposit amount and timestamp
    - withdraw: return principal + accrued yield

    - _Requirements: 5.1, 5.2_
  - [x] 6.3 Implement mock lending view functions
    - get_balance: return current principal
    - get_pending_yield: calculate yield using APY and time elapsed





    - get_total_with_yield: return (principal, yield) tuple
    - _Requirements: 5.3, 5.4_


- [x] 7. Implement No-Loss Market Type

  - [x] 7.1 Add MARKET_TYPE_NO_LOSS constant and handling
    - Update create_market to accept market_type parameter

    - Flag market as principal-protected when type is NO_LOSS
    - _Requirements: 6.1_
  - [x] 7.2 Update place_vote for no-loss markets
    - Deposit stake to mock lending protocol for NO_LOSS markets
    - _Requirements: 6.2_
  - [x] 7.3 Update resolve for no-loss markets
    - Withdraw all funds + yield from lending protocol on resolution
    - _Requirements: 6.3_
  - [x] 7.4 Update claim_reward for no-loss markets
    - Return principal to ALL voters (winners and losers)
    - Distribute yield rewards to winners only (proportional to stake)
    - _Requirements: 6.4, 6.5_



- [x] 8. Checkpoint - No-Loss Tests
  - Test no-loss market full cycle with mock lending
  - Verify principal protection for losers
  - Verify yield distribution to winners
  - Ensure all tests pass, ask the user if questions arise







## Phase 6: Deployment & Integration


- [x] 9. Deploy to Movement Devnet
  - [x] 9.1 Compile and verify all contracts
    - Run `movement move compile`
    - Fix any compilation errors

    - _Requirements: All_
  - [x] 9.2 Deploy contracts to devnet
    - Deploy market module with admin account
    - Deploy mock lending module (if P1 complete)


    - Verify deployment and initialize
    - Document deployed contract addresses
    - _Requirements: 1.1_
  - [x] 9.3 Test on devnet
    - Create test market
    - Place test votes from multiple accounts
    - Resolve market and verify outcome
    - Claim rewards and verify amounts
    - _Requirements: All P0_

- [x] 10. Backend Integration
  - [x] 10.1 Add Movement SDK to backend

    - Install @aptos-labs/ts-sdk or equivalent Movement SDK
    - Update package.json with blockchain dependencies
    - _Requirements: All_


  - [x] 10.2 Create contract service
    - Create `backend/src/services/contract.service.ts`
    - Implement contract interaction functions (createMarket, placeVote, resolve, claimReward)
    - Use MOVEMENT_RPC_URL and MOVEMENT_PRIVATE_KEY from env config

    - _Requirements: All_
  - [x] 10.3 Update prediction controller for on-chain operations

    - Call contract service on create market
    - Call contract service on vote
    - Call contract service on resolve
    - Call contract service on claim
    - _Requirements: All_

  - [x] 10.4 Add event listener for on-chain sync (optional)
    - Listen to contract events
    - Sync on-chain data to database
    - _Requirements: 8.1_


- [x] 11. Final Checkpoint

  - End-to-end test: frontend → backend → contract → backend → frontend
  - Verify all flows work on-chain
  - Document contract addresses and ABIs
  - Ensure all tests pass, ask the user if questions arise
