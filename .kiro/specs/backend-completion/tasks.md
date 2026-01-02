# Implementation Plan

- [x] 1. Setup Predictions Module Structure
  - [x] 1.1 Create predictions validator with Zod schemas
    - Define CreateMarketInput, ListMarketsQuery, PlaceVoteInput, ResolveMarketInput schemas
    - Add validation for endDate (must be future), stake amounts, enum values
    - _Requirements: 1.5, 3.5, 3.6_
  - [x] 1.2 Create predictions routes file
    - Setup router with all prediction endpoints
    - Apply auth middleware and validation middleware
    - _Requirements: 1.1, 2.1, 3.1, 4.1, 5.1_
  - [x] 1.3 Register predictions routes in main router
    - Add `/api/predictions` route to index.ts
    - _Requirements: 1.1_

- [x] 2. Implement Prediction Market CRUD
  - [x] 2.1 Implement createMarket controller
    - Verify user is group member
    - Create market with ACTIVE status, 50/50 percentages
    - Return created market with creator info
    - _Requirements: 1.1, 1.2, 1.3, 1.4_
  - [ ] 2.2 Write property test for market creation invariants (SKIPPED - MVP)
    - **Property 1: Market Creation Invariants**
    - **Validates: Requirements 1.1, 1.3**
  - [x] 2.3 Implement getMarkets controller (list)
    - Support groupId filter, status filter, pagination
    - Include market stats in response
    - Support sorting by createdAt, totalVolume, endDate
    - _Requirements: 2.1, 2.3, 2.4_
  - [x] 2.4 Implement getMarketById controller
    - Return full market details with votes and creator
    - Include user's vote if authenticated
    - _Requirements: 2.2_

- [x] 3. Checkpoint - Verify Market CRUD
  - All controllers implemented and routes registered

- [x] 4. Implement Voting System
  - [x] 4.1 Implement placeVote controller
    - Verify market is ACTIVE
    - Verify user hasn't voted already
    - Validate stake amount against min/max
    - Create vote and update market pools
    - Recalculate percentages
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 3.6_
  - [ ] 4.2-4.4 Property tests (SKIPPED - MVP)

- [x] 5. Checkpoint - Verify Voting System
  - placeVote controller implemented

- [x] 6. Implement Market Resolution
  - [x] 6.1 Reward calculation inline in resolveMarket
    - Rewards distributed proportionally to stake
    - Refund for INVALID outcome
    - _Requirements: 4.4, 4.5_
  - [x] 6.2 Implement resolveMarket controller
    - Verify user is JUDGE or ADMIN in group
    - Verify market endDate has passed
    - Update market status and outcome
    - Calculate and store rewards for all voters
    - Update user stats (totalPredictions, correctPredictions)
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 8.3_
  - [ ] 6.3-6.5 Property tests (SKIPPED - MVP)

- [x] 7. Checkpoint - Verify Resolution System
  - resolveMarket controller implemented

- [x] 8. Implement Claim Rewards
  - [x] 8.1 Implement claimReward controller
    - Verify market is RESOLVED
    - Verify user voted on winning side (or INVALID)
    - Verify not already claimed
    - Mark hasClaimedReward true
    - Return reward amount
    - _Requirements: 5.1, 5.2, 5.3, 5.4_
  - [ ] 8.2 Property test (SKIPPED - MVP)

- [x] 9. Implement Mock Yield System
  - [x] 9.1-9.2 Mock yield calculation in getUserVotes
    - Calculate yield based on vote createdAt to now (5% APY)
    - Include in getUserVotes response
    - _Requirements: 7.1, 7.2_
  - [ ] 9.3 Property test (SKIPPED - MVP)

- [x] 10. Implement User Stats & History
  - [x] 10.1 Implement getUserVotes controller
    - Return vote history with market details and mock yield
    - _Requirements: 8.1_
  - [x] 10.2 Stats calculation in resolution
    - Increment totalPredictions for all voters
    - Increment correctPredictions for winners
    - _Requirements: 8.3_
  - [ ] 10.3 Property test (SKIPPED - MVP)

- [x] 11. Checkpoint - Verify Stats System
  - getUserVotes and stats update implemented

- [x] 12. Implement Subscription System (Mock for Hackathon)
  - [x] 12.1 Create subscriptions validator
    - Define CheckoutInput, WebhookInput schemas
    - _Requirements: 6.1_
  - [x] 12.2 Create subscriptions routes and controller
    - POST /checkout - Return mock checkout URL
    - GET /status - Return user's pro status
    - POST /webhook - Mock webhook to activate pro
    - _Requirements: 6.1, 6.2, 6.3_
  - [x] 12.3 Pro expiration check in getStatus
    - Check if proExpiresAt is in the past
    - _Requirements: 6.4_

- [x] 13. Final Checkpoint - Update Postman & Deploy
  - [x] Updated Postman collection with prediction endpoints
  - [x] Updated Postman collection with subscription endpoints
  - [ ] Redeploy to Render (user action required)
