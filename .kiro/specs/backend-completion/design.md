# Design Document

## Overview

This design document outlines the implementation of remaining backend features for Predictly. The system extends the existing Express.js + Prisma backend with prediction market management, voting, resolution, rewards, and subscription features.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      API Layer                               │
│  /api/predictions, /api/subscriptions                       │
└─────────────────────┬───────────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────────┐
│                   Controller Layer                           │
│  predictions.controller.ts, subscriptions.controller.ts     │
└─────────────────────┬───────────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────────┐
│                   Service Layer                              │
│  prediction.service.ts, reward.service.ts, yield.service.ts │
└─────────────────────┬───────────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────────┐
│                   Data Layer (Prisma)                        │
│  PredictionMarket, Vote, User                               │
└─────────────────────────────────────────────────────────────┘
```

## Components and Interfaces

### 1. Predictions Controller

```typescript
// POST /api/predictions - Create market
interface CreateMarketInput {
  groupId: string;
  title: string;
  description?: string;
  imageUrl?: string;
  marketType: 'STANDARD' | 'NO_LOSS' | 'WITH_YIELD';
  endDate: string; // ISO date
  minStake: number;
  maxStake?: number;
}

// GET /api/predictions - List markets
interface ListMarketsQuery {
  groupId?: string;
  status?: 'ACTIVE' | 'PENDING' | 'RESOLVED';
  page?: number;
  limit?: number;
  sortBy?: 'createdAt' | 'totalVolume' | 'endDate';
}

// POST /api/predictions/:id/vote - Place vote
interface PlaceVoteInput {
  prediction: 'YES' | 'NO';
  amount: number;
}

// POST /api/predictions/:id/resolve - Resolve market
interface ResolveMarketInput {
  outcome: 'YES' | 'NO' | 'INVALID';
  resolutionNote?: string;
}

// POST /api/predictions/:id/claim - Claim reward
// No body required
```

### 2. Subscriptions Controller

```typescript
// POST /api/subscriptions/checkout
interface CheckoutInput {
  plan: 'monthly' | 'yearly';
}

interface CheckoutResponse {
  checkoutUrl: string;
  sessionId: string;
}

// GET /api/subscriptions/status
interface SubscriptionStatus {
  isPro: boolean;
  proExpiresAt: string | null;
  plan: string | null;
}
```

### 3. Reward Service

```typescript
interface RewardCalculation {
  calculateRewards(marketId: string, outcome: 'YES' | 'NO'): Promise<void>;
  calculateRefunds(marketId: string): Promise<void>;
  getRewardAmount(voteId: string): Promise<number>;
}
```

### 4. Yield Service (Mock)

```typescript
interface YieldService {
  calculateMockYield(amount: number, stakeDays: number): number;
  getYieldForVote(voteId: string): Promise<number>;
}

// Mock APY: 5% annual = 0.0137% daily
const MOCK_DAILY_YIELD_RATE = 0.05 / 365;
```

## Data Models

### Existing Models (No Changes)
- User, Group, GroupMember, PredictionMarket, Vote - Already defined in schema.prisma

### API Response Models

```typescript
interface MarketResponse {
  id: string;
  title: string;
  description: string | null;
  imageUrl: string | null;
  marketType: string;
  status: string;
  outcome: string | null;
  endDate: string;
  minStake: number;
  maxStake: number | null;
  totalVolume: number;
  yesPool: number;
  noPool: number;
  yesPercentage: number;
  noPercentage: number;
  participantCount: number;
  creator: {
    id: string;
    displayName: string;
    avatarUrl: string | null;
  };
  group: {
    id: string;
    name: string;
  };
  userVote?: {
    prediction: string;
    amount: number;
    hasClaimedReward: boolean;
    rewardAmount: number | null;
  };
  createdAt: string;
}

interface VoteResponse {
  id: string;
  prediction: string;
  amount: number;
  hasClaimedReward: boolean;
  rewardAmount: number | null;
  mockYield: number;
  market: {
    id: string;
    title: string;
    status: string;
    outcome: string | null;
  };
  createdAt: string;
}
```



## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system-essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Market Creation Invariants
*For any* valid market creation request from a group member, the created market SHALL have status ACTIVE, yesPercentage = 50, noPercentage = 50, totalVolume = 0, and participantCount = 0.
**Validates: Requirements 1.1, 1.3**

### Property 2: Authorization Enforcement
*For any* user attempting to create a market in a group they are not a member of, the system SHALL return a 403 forbidden error.
**Validates: Requirements 1.2**

### Property 3: Pool Percentage Calculation
*For any* market with votes, yesPercentage SHALL equal (yesPool / (yesPool + noPool)) * 100, and noPercentage SHALL equal 100 - yesPercentage.
**Validates: Requirements 3.4**

### Property 4: Vote Uniqueness
*For any* user and market combination, at most one vote record SHALL exist. Duplicate vote attempts SHALL be rejected.
**Validates: Requirements 3.3**

### Property 5: Stake Validation
*For any* vote attempt, if amount < minStake OR (maxStake is set AND amount > maxStake), the system SHALL reject the vote.
**Validates: Requirements 3.5, 3.6**

### Property 6: Resolution Authorization
*For any* market resolution attempt, if the user does not have JUDGE or ADMIN role in the market's group, the system SHALL return 403 forbidden.
**Validates: Requirements 4.2**

### Property 7: Resolution Timing
*For any* market resolution attempt before the market's endDate, the system SHALL reject with timing error.
**Validates: Requirements 4.3**

### Property 8: Reward Calculation Correctness
*For any* resolved market with outcome YES or NO, the sum of all winning voter rewards SHALL equal the total pool (yesPool + noPool).
**Validates: Requirements 4.4**

### Property 9: Invalid Resolution Refund
*For any* market resolved as INVALID, all voters SHALL have rewardAmount equal to their original vote amount.
**Validates: Requirements 4.5**

### Property 10: Claim Eligibility
*For any* claim attempt, the system SHALL only allow claims where: market is RESOLVED, user voted on winning side (or INVALID), and hasClaimedReward is false.
**Validates: Requirements 5.1, 5.2, 5.3, 5.4**

### Property 11: Mock Yield Calculation
*For any* vote, mockYield SHALL equal amount * DAILY_RATE * daysSinceVote, where DAILY_RATE = 0.05/365.
**Validates: Requirements 7.2**

### Property 12: Stats Update on Resolution
*For any* market resolution, all participating users SHALL have totalPredictions incremented by 1, and winning users SHALL have correctPredictions incremented by 1.
**Validates: Requirements 8.3**

## Error Handling

### HTTP Status Codes
- 200: Success
- 201: Created
- 400: Bad Request (validation errors)
- 401: Unauthorized (no/invalid token)
- 403: Forbidden (not authorized for action)
- 404: Not Found
- 409: Conflict (duplicate vote, already claimed)
- 500: Internal Server Error

### Error Response Format
```typescript
interface ErrorResponse {
  success: false;
  error: {
    code: string;
    message: string;
    details?: Record<string, string[]>; // field-level validation errors
  };
}
```

### Error Codes
- `MARKET_NOT_FOUND`: Market does not exist
- `NOT_GROUP_MEMBER`: User is not a member of the group
- `NOT_AUTHORIZED`: User lacks required role
- `MARKET_NOT_ACTIVE`: Cannot vote on non-active market
- `ALREADY_VOTED`: User already voted on this market
- `INVALID_STAKE_AMOUNT`: Amount below min or above max
- `MARKET_NOT_ENDED`: Cannot resolve before endDate
- `MARKET_NOT_RESOLVED`: Cannot claim on unresolved market
- `ALREADY_CLAIMED`: Reward already claimed
- `NOT_ELIGIBLE`: User not eligible for reward

## Testing Strategy

### Unit Testing
- Use Jest as the testing framework
- Test individual service functions in isolation
- Mock Prisma client for database operations
- Test validation logic with edge cases

### Property-Based Testing
- Use fast-check library for property-based tests
- Generate random valid/invalid inputs
- Verify invariants hold across all generated cases
- Minimum 100 iterations per property test

### Test File Structure
```
backend/src/
├── __tests__/
│   ├── predictions.controller.test.ts
│   ├── predictions.service.test.ts
│   ├── reward.service.test.ts
│   ├── yield.service.test.ts
│   └── properties/
│       ├── market.properties.test.ts
│       ├── vote.properties.test.ts
│       └── reward.properties.test.ts
```

### Key Test Scenarios
1. Market creation with valid/invalid data
2. Vote placement with stake validation
3. Pool percentage recalculation after votes
4. Resolution by judge vs non-judge
5. Reward calculation for winners
6. Claim flow for eligible/ineligible users
7. Mock yield calculation accuracy
