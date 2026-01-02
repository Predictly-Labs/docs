# Design Document

## Overview

This design covers the implementation of missing API endpoints for the Predictly backend. The features are organized into three priority tiers (Critical, High Priority, and Polish) to enable incremental delivery.

## Architecture

### API Layer Structure

```
Routes (Express Router)
    ↓
Validators (Zod Schemas)
    ↓
Controllers (Request Handlers)
    ↓
Prisma ORM (Database Queries)
    ↓
PostgreSQL Database
```

### New Endpoints

**Critical (Sprint 1):**
- `GET /api/groups/my-groups` - List user's groups
- `GET /api/predictions/my-votes` (enhanced) - Paginated votes
- `GET /api/predictions/:marketId/my-vote` - Check specific vote

**High Priority (Sprint 2):**
- `GET /api/groups/:id/members?role=JUDGE` - Filter members
- `GET /api/predictions/my-votes/stats` - Aggregate statistics
- `GET /api/markets?marketType=NO_LOSS` - Filter by type

**Polish (Sprint 3):**
- `GET /api/groups/:id/settings` - Get group settings
- `PUT /api/groups/:id/settings` - Update group settings
- `GET /api/users/:userId/resolved-markets` - Judge history
- `POST /api/groups/:groupId/judges/bulk` - Bulk judge assignment

## Components and Interfaces

### 1. My Groups Endpoint

**Route:** `GET /api/groups/my-groups`

**Query Parameters:**
```typescript
interface MyGroupsQuery {
  page: number;        // default: 1
  limit: number;       // default: 20, max: 100
  role?: 'ADMIN' | 'JUDGE' | 'MODERATOR' | 'MEMBER';
  search?: string;
  sort?: 'recent' | 'active' | 'members';
}
```

**Response:**
```typescript
interface MyGroupsResponse {
  success: true;
  data: Array<{
    id: string;
    name: string;
    iconUrl: string | null;
    userRole: GroupRole;
    joinedAt: Date;
    stats: {
      memberCount: number;
      activeMarkets: number;
      totalVolume: number;
    };
  }>;
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}
```

**Database Query:**
```typescript
prisma.groupMember.findMany({
  where: {
    userId: currentUser.id,
    ...(role && { role }),
    ...(search && {
      group: {
        name: { contains: search, mode: 'insensitive' }
      }
    })
  },
  include: {
    group: {
      include: {
        _count: { select: { members: true, markets: true } },
        markets: { 
          where: { status: 'ACTIVE' },
          select: { totalVolume: true }
        }
      }
    }
  },
  skip: (page - 1) * limit,
  take: limit,
  orderBy: getOrderBy(sort)
})
```



### 2. Enhanced My Votes Endpoint

**Route:** `GET /api/predictions/my-votes`

**Query Parameters:**
```typescript
interface MyVotesQuery {
  page: number;        // default: 1
  limit: number;       // default: 20, max: 100
  status?: 'ACTIVE' | 'RESOLVED' | 'PENDING';
  groupId?: string;
  outcome?: 'won' | 'lost' | 'pending';
}
```

**Response:**
```typescript
interface MyVotesResponse {
  success: true;
  data: Array<{
    id: string;
    marketId: string;
    prediction: 'YES' | 'NO';
    amount: number;
    hasClaimedReward: boolean;
    rewardAmount: number | null;
    mockYield: number;
    daysSinceVote: number;
    createdAt: Date;
    market: {
      id: string;
      title: string;
      status: MarketStatus;
      outcome: MarketOutcome | null;
      endDate: Date;
    };
  }>;
  pagination: PaginationMeta;
}
```

### 3. Check My Vote Endpoint

**Route:** `GET /api/predictions/:marketId/my-vote`

**Response:**
```typescript
interface MyVoteResponse {
  success: true;
  data: {
    id: string;
    marketId: string;
    prediction: 'YES' | 'NO';
    amount: number;
    createdAt: Date;
    hasClaimedReward: boolean;
    rewardAmount: number | null;
  } | null;
}
```

**Database Query:**
```typescript
prisma.vote.findUnique({
  where: {
    marketId_userId: {
      marketId,
      userId: currentUser.id
    }
  },
  include: {
    market: {
      select: { id: true, title: true, status: true, outcome: true }
    }
  }
})
```

### 4. My Votes Statistics

**Route:** `GET /api/predictions/my-votes/stats`

**Response:**
```typescript
interface VotesStatsResponse {
  success: true;
  data: {
    totalVotes: number;
    totalInvested: number;
    totalEarnings: number;
    roi: number;
    winRate: number;
    activeVotes: number;
    resolvedVotes: number;
    wonVotes: number;
    lostVotes: number;
    averageStake: number;
    byGroup: Array<{
      groupId: string;
      groupName: string;
      votes: number;
      earnings: number;
    }>;
  };
}
```

**Calculation Logic:**
```typescript
// Fetch all user votes
const votes = await prisma.vote.findMany({
  where: { userId },
  include: { market: { include: { group: true } } }
});

// Calculate aggregates
const totalInvested = votes.reduce((sum, v) => sum + v.amount, 0);
const totalEarnings = votes.reduce((sum, v) => sum + (v.rewardAmount || 0), 0);
const resolvedVotes = votes.filter(v => v.market.status === 'RESOLVED');
const wonVotes = resolvedVotes.filter(v => 
  (v.prediction === 'YES' && v.market.outcome === 'YES') ||
  (v.prediction === 'NO' && v.market.outcome === 'NO')
);

const stats = {
  totalVotes: votes.length,
  totalInvested,
  totalEarnings,
  roi: (totalEarnings - totalInvested) / totalInvested,
  winRate: wonVotes.length / resolvedVotes.length,
  activeVotes: votes.filter(v => v.market.status === 'ACTIVE').length,
  resolvedVotes: resolvedVotes.length,
  wonVotes: wonVotes.length,
  lostVotes: resolvedVotes.length - wonVotes.length,
  averageStake: totalInvested / votes.length,
  byGroup: groupByGroup(votes)
};
```

### 5. Group Settings

**Database Schema Addition:**
```prisma
model Group {
  // ... existing fields
  
  // Settings
  defaultMarketType MarketType @default(STANDARD)
  allowedMarketTypes MarketType[] @default([STANDARD, NO_LOSS])
}
```

**Routes:**
- `GET /api/groups/:id/settings`
- `PUT /api/groups/:id/settings` (Admin only)

**Request/Response:**
```typescript
interface GroupSettings {
  defaultMarketType: 'STANDARD' | 'NO_LOSS' | 'WITH_YIELD';
  allowedMarketTypes: Array<'STANDARD' | 'NO_LOSS' | 'WITH_YIELD'>;
}
```

## Data Models

### Existing Models (No Changes)
- User
- Group
- GroupMember
- PredictionMarket
- Vote

### Modified Models

**Group Model (Sprint 3):**
```prisma
model Group {
  id          String  @id @default(uuid())
  name        String
  description String?
  iconUrl     String?
  inviteCode  String  @unique
  isPublic    Boolean @default(true)
  
  // NEW: Settings
  defaultMarketType  MarketType   @default(STANDARD)
  allowedMarketTypes MarketType[] @default([STANDARD, NO_LOSS])
  
  createdById String
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
  
  createdBy User               @relation("GroupCreator", fields: [createdById], references: [id])
  members   GroupMember[]
  markets   PredictionMarket[]
}
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: My Groups Completeness
*For any* authenticated user, querying their groups should return all and only the groups where they are a member.

**Validates: Requirements 1.1**

### Property 2: Pagination Consistency
*For any* paginated endpoint, the sum of all pages should equal the total count, and no items should be duplicated or missing across pages.

**Validates: Requirements 1.5, 2.1, 7.1**

### Property 3: Role Filter Correctness
*For any* role filter applied to group members, all returned members should have exactly that role, and no members with that role should be excluded.

**Validates: Requirements 1.2, 4.1**

### Property 4: Vote Uniqueness
*For any* market and user combination, there should be at most one vote record.

**Validates: Requirements 2.5**

### Property 5: Statistics Accuracy
*For any* user's vote statistics, the sum of won votes and lost votes should equal the total resolved votes.

**Validates: Requirements 3.1, 3.2**

### Property 6: Win Rate Calculation
*For any* user with resolved votes, the win rate should be between 0 and 1, and should equal wonVotes / resolvedVotes.

**Validates: Requirements 3.2**

### Property 7: ROI Calculation
*For any* user with votes, ROI should equal (totalEarnings - totalInvested) / totalInvested.

**Validates: Requirements 3.2**

### Property 8: Judge Authorization
*For any* market resolution attempt, only users with JUDGE or ADMIN role in the market's group should be able to resolve it.

**Validates: Requirements 4.5**

### Property 9: Market Type Filter
*For any* market type filter, all returned markets should have exactly that market type.

**Validates: Requirements 5.1**

### Property 10: Settings Persistence
*For any* group settings update, subsequent reads should return the updated values.

**Validates: Requirements 6.1, 6.5**

### Property 11: Admin Authorization
*For any* group settings modification, only users with ADMIN role should be able to update settings.

**Validates: Requirements 6.4**

### Property 12: Response Format Consistency
*For all* list endpoints, responses should include success, data, and pagination fields in the same structure.

**Validates: Requirements 7.2**

## Error Handling

### Validation Errors (400)
- Invalid query parameters
- Invalid pagination values (page < 1, limit > 100)
- Invalid enum values (role, status, marketType)

### Authentication Errors (401)
- Missing or invalid JWT token
- Expired token

### Authorization Errors (403)
- Non-admin trying to update group settings
- Non-judge trying to resolve market
- User trying to access private group they're not member of

### Not Found Errors (404)
- Group not found
- Market not found
- User not found
- Vote not found

### Server Errors (500)
- Database connection failures
- Unexpected errors during query execution

## Testing Strategy

### Unit Tests
- Test each controller function with mocked Prisma client
- Test validator schemas with valid and invalid inputs
- Test calculation functions (ROI, win rate, statistics)
- Test authorization logic

### Property-Based Tests
- Test pagination consistency across random page sizes
- Test role filtering with random role assignments
- Test statistics calculations with random vote data
- Test market type filtering with random market types

### Integration Tests
- Test complete request/response cycle for each endpoint
- Test authentication middleware integration
- Test database queries with real test database
- Test error handling for various failure scenarios

### Performance Tests
- Test response time for paginated endpoints with large datasets
- Test database query performance with indexes
- Test concurrent requests to same endpoints

## Performance Considerations

### Database Indexes
```sql
-- Add indexes for frequently queried fields
CREATE INDEX idx_group_member_user_role ON "GroupMember"(userId, role);
CREATE INDEX idx_vote_user_market ON "Vote"(userId, marketId);
CREATE INDEX idx_market_type_status ON "PredictionMarket"(marketType, status);
CREATE INDEX idx_group_member_joined ON "GroupMember"(userId, joinedAt DESC);
```

### Caching Strategy
- Cache user statistics for 5 minutes (Redis or in-memory)
- Cache group settings for 10 minutes
- Invalidate cache on relevant updates

### Query Optimization
- Use `select` to limit returned fields
- Use `include` strategically to avoid N+1 queries
- Use `_count` for aggregations instead of fetching all records
- Implement cursor-based pagination for very large datasets

### Response Time Targets
- List endpoints: < 500ms
- Single item endpoints: < 200ms
- Statistics endpoints: < 1000ms (with caching)
- Update endpoints: < 300ms

