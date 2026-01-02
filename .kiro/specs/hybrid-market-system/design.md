# Design Document

## Overview

This design implements a hybrid prediction market system that balances cost efficiency with decentralization. Markets are created off-chain in the backend database for free, but voting and financial operations happen on-chain for trustless execution. The system uses lazy initialization where markets are only deployed to the blockchain when the first vote is placed, minimizing gas costs while maintaining security for financial transactions.

The design addresses three major components:
1. **Wallet-Based Authentication**: Replacing Privy authentication with signature-based wallet authentication
2. **Lazy Market Initialization**: Off-chain market creation with on-demand blockchain deployment
3. **Relay Wallet System**: Backend-managed wallet for paying initialization gas fees

## Architecture

### High-Level Architecture

```
┌─────────────┐         ┌─────────────┐         ┌──────────────┐
│   Frontend  │────────▶│   Backend   │────────▶│   Movement   │
│   (Next.js) │         │  (Express)  │         │   Network    │
└─────────────┘         └─────────────┘         └──────────────┘
      │                       │                         │
      │                       │                         │
      ▼                       ▼                         ▼
  User Wallet          PostgreSQL DB            Smart Contract
  (Petra, etc)         (Metadata)               (Money Logic)
```

### Component Responsibilities

**Frontend:**
- Wallet connection and signature generation
- Check market status (PENDING vs ACTIVE)
- Trigger initialization when needed
- Submit vote transactions to blockchain
- Display combined data (backend metadata + on-chain stats)

**Backend:**
- Authenticate users via wallet signatures
- Store market metadata and user data
- Initialize markets on-chain via relay wallet
- Sync on-chain data to database cache
- Provide REST API for all operations

**Smart Contract:**
- Handle vote transactions and money
- Maintain market pools and percentages
- Execute payouts and rewards
- Provide view functions for data queries

## Components and Interfaces

### 1. Authentication Service

**Purpose**: Handle wallet-based authentication using signature verification.

**Interface:**
```typescript
interface AuthService {
  // Generate a sign-in message with nonce
  generateSignInMessage(walletAddress: string): Promise<{
    message: string;
    nonce: string;
    expiresAt: Date;
  }>;
  
  // Verify signature and create/retrieve user
  verifySignature(
    walletAddress: string,
    signature: string,
    message: string
  ): Promise<{
    user: User;
    token: string;
  }>;
  
  // Validate JWT token
  validateToken(token: string): Promise<User>;
}
```

**Implementation Details:**
- Generate unique nonce per sign-in attempt (stored in Redis with TTL)
- Message format: `Sign in to Predictly\n\nWallet: {address}\nNonce: {nonce}\nTimestamp: {timestamp}`
- Use `@aptos-labs/ts-sdk` for signature verification
- Generate JWT with 7-day expiration
- Store nonce in Redis to prevent replay attacks

### 2. Market Service

**Purpose**: Manage market lifecycle from creation to resolution.

**Interface:**
```typescript
interface MarketService {
  // Create market in database (off-chain)
  createMarket(data: CreateMarketInput): Promise<Market>;
  
  // Initialize market on blockchain
  initializeMarket(marketId: string): Promise<{
    onChainId: string;
    txHash: string;
  }>;
  
  // Get market with on-chain data if available
  getMarket(marketId: string): Promise<MarketWithStats>;
  
  // Sync on-chain data to database
  syncMarketData(marketId: string): Promise<void>;
  
  // Get markets by group
  getGroupMarkets(groupId: string, filters?: MarketFilters): Promise<Market[]>;
}
```

**State Machine:**
```
PENDING ──initialize──▶ ACTIVE ──resolve──▶ RESOLVED
   │                       │
   └──────cancel──────────┴──────────────▶ CANCELLED
```

### 3. Relay Wallet Service

**Purpose**: Manage backend wallet for paying gas fees.

**Interface:**
```typescript
interface RelayWalletService {
  // Get wallet address
  getAddress(): string;
  
  // Get current balance
  getBalance(): Promise<number>;
  
  // Create market on-chain
  createMarketOnChain(params: OnChainMarketParams): Promise<{
    onChainId: string;
    txHash: string;
  }>;
  
  // Check if wallet has sufficient balance
  hasSufficientBalance(estimatedGas: number): Promise<boolean>;
}
```

**Implementation Details:**
- Load private key from `RELAY_WALLET_PRIVATE_KEY` environment variable
- Monitor balance and log warnings when below threshold (e.g., 10 MOVE)
- Use `@aptos-labs/ts-sdk` for transaction signing
- Implement retry logic for failed transactions (max 3 attempts)
- Log all transactions for audit trail

### 4. Contract Service

**Purpose**: Interface with Movement Network smart contract.

**Interface:**
```typescript
interface ContractService {
  // View functions (read-only)
  getMarketData(onChainId: string): Promise<OnChainMarketData>;
  getUserVote(onChainId: string, userAddress: string): Promise<Vote | null>;
  
  // Build transaction payloads (for frontend to sign)
  buildPlaceVotePayload(
    onChainId: string,
    prediction: 'YES' | 'NO',
    amount: number
  ): TransactionPayload;
  
  buildResolveMarketPayload(
    onChainId: string,
    outcome: 'YES' | 'NO' | 'INVALID'
  ): TransactionPayload;
  
  buildClaimRewardPayload(onChainId: string): TransactionPayload;
}
```

**Data Conversion:**
- MOVE to Octas: `multiply by 100_000_000`
- Octas to MOVE: `divide by 100_000_000`
- Percentage to Basis Points: `multiply by 100`
- Basis Points to Percentage: `divide by 100`

### 5. Initialization Coordinator

**Purpose**: Coordinate market initialization with race condition handling.

**Interface:**
```typescript
interface InitializationCoordinator {
  // Initialize market with locking
  initializeMarket(marketId: string): Promise<InitializationResult>;
  
  // Check if initialization is in progress
  isInitializing(marketId: string): Promise<boolean>;
}
```

**Implementation Details:**
- Use PostgreSQL advisory locks to prevent concurrent initialization
- Lock pattern: `pg_advisory_xact_lock(hashtext(marketId))`
- Transaction flow:
  1. Acquire lock
  2. Check if already initialized
  3. If not, create on-chain
  4. Update database with onChainId
  5. Release lock (automatic on transaction commit)

## Data Models

### User Model (Updated)

```typescript
interface User {
  id: string;                    // UUID
  walletAddress: string;         // Primary identifier (unique, required)
  privyId?: string | null;       // Legacy field (nullable for migration)
  displayName?: string;
  avatarUrl?: string;
  
  // Stats
  totalPredictions: number;
  correctPredictions: number;
  totalEarnings: number;
  currentStreak: number;
  
  // Subscription
  isPro: boolean;
  proExpiresAt?: Date;
  
  createdAt: Date;
  updatedAt: Date;
}
```

### Market Model (Updated)

```typescript
interface Market {
  id: string;                    // UUID (backend ID)
  onChainId?: string | null;     // Blockchain ID (null if PENDING)
  
  groupId: string;
  title: string;
  description?: string;
  imageUrl?: string;
  
  // Market config
  marketType: 'STANDARD' | 'NO_LOSS' | 'WITH_YIELD';
  endDate: Date;
  minStake: number;              // In MOVE
  maxStake?: number;             // In MOVE
  
  // Status
  status: 'PENDING' | 'ACTIVE' | 'RESOLVED' | 'CANCELLED';
  outcome?: 'YES' | 'NO' | 'INVALID';
  
  // Cached on-chain stats
  totalVolume: number;
  yesPool: number;
  noPool: number;
  yesPercentage: number;
  noPercentage: number;
  participantCount: number;
  
  // Resolution
  resolvedById?: string;
  resolvedAt?: Date;
  resolutionNote?: string;
  
  createdById: string;
  createdAt: Date;
  updatedAt: Date;
}
```

### Initialization Lock Model (New)

```typescript
interface InitializationLock {
  marketId: string;              // Primary key
  lockedAt: Date;
  lockedBy: string;              // Process/instance ID
  expiresAt: Date;               // Auto-expire after 5 minutes
}
```

## API Endpoints

### Authentication Endpoints

```
POST /api/auth/wallet/message
Request: { walletAddress: string }
Response: { message: string, nonce: string, expiresAt: string }

POST /api/auth/wallet/verify
Request: { walletAddress: string, signature: string, message: string }
Response: { user: User, token: string }

GET /api/auth/me
Headers: Authorization: Bearer <token>
Response: { user: User }
```

### Market Endpoints

```
POST /api/markets
Headers: Authorization: Bearer <token>
Request: {
  groupId: string,
  title: string,
  description?: string,
  imageUrl?: string,
  marketType: 'STANDARD' | 'NO_LOSS' | 'WITH_YIELD',
  endDate: string,
  minStake: number,
  maxStake?: number
}
Response: { market: Market }

POST /api/markets/:id/initialize
Headers: Authorization: Bearer <token>
Response: {
  onChainId: string,
  txHash: string,
  status: 'ACTIVE'
}

GET /api/markets/:id
Response: {
  market: Market,
  onChainData?: OnChainMarketData
}

GET /api/groups/:groupId/markets
Query: ?status=PENDING|ACTIVE|RESOLVED
Response: { markets: Market[] }

POST /api/markets/:id/sync
Headers: Authorization: Bearer <token>
Response: { market: Market }
```

### Relay Wallet Endpoints (Admin Only)

```
GET /api/admin/relay-wallet/balance
Headers: Authorization: Bearer <admin-token>
Response: { balance: number, address: string }

GET /api/admin/relay-wallet/transactions
Headers: Authorization: Bearer <admin-token>
Response: { transactions: Transaction[] }
```

## Workflows

### Workflow 1: User Authentication

```
1. User connects wallet in frontend (Petra, Martian, etc)
2. Frontend → Backend: POST /api/auth/wallet/message
   - Backend generates nonce and message
   - Backend stores nonce in Redis (TTL: 5 minutes)
3. Frontend prompts user to sign message
4. User signs message with wallet
5. Frontend → Backend: POST /api/auth/wallet/verify
   - Backend verifies signature using Aptos SDK
   - Backend checks nonce is valid and not used
   - Backend finds or creates user by walletAddress
   - Backend generates JWT token
6. Frontend stores token and user data
7. Frontend includes token in Authorization header for subsequent requests
```

### Workflow 2: Create Market (Off-Chain)

```
1. User fills market creation form
2. Frontend → Backend: POST /api/markets
   - Backend validates input
   - Backend creates market in database
   - Backend sets status = PENDING, onChainId = null
   - Backend associates market with groupId
3. Backend → Frontend: Returns market with ID
4. Frontend displays market as "Not yet on-chain"
```

### Workflow 3: First Vote (Lazy Initialization)

```
1. User clicks vote on PENDING market
2. Frontend checks: market.onChainId === null
3. Frontend → Backend: POST /api/markets/:id/initialize
   - Backend acquires advisory lock on marketId
   - Backend checks if already initialized (race condition)
   - If not initialized:
     a. Backend loads relay wallet
     b. Backend checks relay wallet balance
     c. Backend builds create_market transaction
     d. Backend signs and submits transaction
     e. Backend waits for transaction confirmation
     f. Backend extracts onChainId from events
     g. Backend updates market: onChainId = <id>, status = ACTIVE
   - Backend releases lock
4. Backend → Frontend: Returns { onChainId, txHash }
5. Frontend → Smart Contract: place_vote(onChainId, prediction, amount)
   - User signs transaction with their wallet
   - User pays gas fee for vote
6. Transaction confirms
7. Frontend → Backend: POST /api/markets/:id/sync (optional)
   - Backend fetches on-chain data
   - Backend updates cached stats
```

### Workflow 4: Subsequent Votes

```
1. User clicks vote on ACTIVE market
2. Frontend checks: market.onChainId !== null
3. Frontend → Smart Contract: place_vote(onChainId, prediction, amount)
   - User signs transaction
   - User pays gas fee
4. Transaction confirms
5. Frontend updates UI with new data
6. Background: Backend periodically syncs on-chain data
```

### Workflow 5: View Group Markets

```
1. User navigates to group page
2. Frontend → Backend: GET /api/groups/:groupId/markets
3. Backend queries database for markets in group
4. For each ACTIVE market:
   - Backend fetches cached on-chain data
   - Or optionally fetches fresh data from blockchain
5. Backend → Frontend: Returns markets with metadata + stats
6. Frontend displays:
   - PENDING markets: "Not yet on-chain" badge
   - ACTIVE markets: Live stats (pools, percentages)
   - RESOLVED markets: Final outcome
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Signature Verification Correctness

*For any* wallet address and message, if a signature is valid according to the Aptos signature verification algorithm, then the authentication SHALL succeed and return a valid JWT token.

**Validates: Requirements 1.2, 1.3**

### Property 2: Market Initialization Idempotence

*For any* market, calling the initialize endpoint multiple times SHALL result in exactly one on-chain market creation, and all calls SHALL return the same onChainId.

**Validates: Requirements 3.6**

### Property 3: Status Transition Validity

*For any* market, the status transitions SHALL follow the valid state machine: PENDING → ACTIVE → RESOLVED/CANCELLED, and no invalid transitions SHALL be allowed.

**Validates: Requirements 2.2, 3.3**

### Property 4: On-Chain ID Uniqueness

*For any* two different markets in the backend database, they SHALL have different onChainId values when initialized, ensuring no collision.

**Validates: Requirements 3.3**

### Property 5: Relay Wallet Balance Sufficiency

*For any* market initialization attempt, if the relay wallet balance is below the estimated gas cost, the initialization SHALL fail with an appropriate error before attempting the transaction.

**Validates: Requirements 4.4**

### Property 6: Data Conversion Consistency

*For any* numeric value, converting from MOVE to octas and back to MOVE SHALL produce the original value (round-trip property).

**Validates: Requirements 11.1, 11.2, 11.4**

### Property 7: Authentication Token Validity

*For any* valid JWT token that has not expired, the token validation SHALL succeed and return the correct user data.

**Validates: Requirements 1.5**

### Property 8: Market Creation Authorization

*For any* market creation request, the system SHALL only allow authenticated users to create markets, and SHALL associate the market with the authenticated user's wallet address.

**Validates: Requirements 12.3**

### Property 9: Concurrent Initialization Safety

*For any* market, if multiple initialization requests arrive concurrently, exactly one SHALL succeed in creating the on-chain market, and others SHALL either wait or receive the existing onChainId.

**Validates: Requirements 9.3**

### Property 10: Vote Prerequisite Enforcement

*For any* vote attempt on a PENDING market, the vote SHALL only proceed after successful initialization, ensuring no votes are placed on non-existent on-chain markets.

**Validates: Requirements 7.1, 7.2**

## Error Handling

### Error Categories

**Authentication Errors:**
- `AUTH_INVALID_SIGNATURE`: Signature verification failed
- `AUTH_EXPIRED_NONCE`: Nonce has expired or been used
- `AUTH_INVALID_TOKEN`: JWT token is invalid or expired
- `AUTH_MISSING_TOKEN`: No authorization token provided

**Market Errors:**
- `MARKET_NOT_FOUND`: Market ID does not exist
- `MARKET_ALREADY_INITIALIZED`: Market is already on-chain
- `MARKET_INITIALIZATION_FAILED`: On-chain creation failed
- `MARKET_INVALID_STATUS`: Operation not allowed in current status
- `MARKET_VALIDATION_ERROR`: Input validation failed

**Relay Wallet Errors:**
- `RELAY_INSUFFICIENT_BALANCE`: Not enough MOVE for gas
- `RELAY_TRANSACTION_FAILED`: Transaction submission failed
- `RELAY_WALLET_NOT_CONFIGURED`: Relay wallet not set up

**Blockchain Errors:**
- `BLOCKCHAIN_NETWORK_ERROR`: Network connection failed
- `BLOCKCHAIN_TRANSACTION_TIMEOUT`: Transaction took too long
- `BLOCKCHAIN_INVALID_RESPONSE`: Unexpected response from chain

### Retry Strategy

**Transient Errors (Retry):**
- Network timeouts
- Temporary RPC failures
- Nonce conflicts

**Retry Configuration:**
- Max attempts: 3
- Backoff: Exponential (1s, 2s, 4s)
- Timeout per attempt: 30s

**Permanent Errors (No Retry):**
- Invalid signatures
- Insufficient balance
- Validation errors
- Already initialized

### Error Response Format

```typescript
interface ErrorResponse {
  error: {
    code: string;           // Error code (e.g., "MARKET_NOT_FOUND")
    message: string;        // Human-readable message
    details?: any;          // Additional context
    retryable: boolean;     // Can client retry?
  }
}
```

## Testing Strategy

### Unit Tests

**Authentication Service:**
- Test signature verification with valid/invalid signatures
- Test nonce generation and validation
- Test JWT token generation and validation
- Test expired nonce handling

**Market Service:**
- Test market creation with valid/invalid data
- Test status transitions
- Test market queries and filtering

**Relay Wallet Service:**
- Test balance checking
- Test transaction building
- Test error handling for insufficient balance

**Contract Service:**
- Test data conversion (MOVE ↔ octas, percentage ↔ basis points)
- Test payload building for different operations

### Property-Based Tests

Each correctness property MUST be implemented as a property-based test with minimum 100 iterations.

**Property 1: Signature Verification**
```typescript
// Feature: hybrid-market-system, Property 1: Signature Verification Correctness
test('valid signatures always authenticate', async () => {
  await fc.assert(
    fc.asyncProperty(
      fc.string(), // random message
      async (message) => {
        const wallet = generateRandomWallet();
        const signature = await wallet.sign(message);
        const result = await authService.verifySignature(
          wallet.address,
          signature,
          message
        );
        expect(result.user.walletAddress).toBe(wallet.address);
        expect(result.token).toBeDefined();
      }
    ),
    { numRuns: 100 }
  );
});
```

**Property 2: Market Initialization Idempotence**
```typescript
// Feature: hybrid-market-system, Property 2: Market Initialization Idempotence
test('multiple initializations return same onChainId', async () => {
  await fc.assert(
    fc.asyncProperty(
      marketGenerator(), // generates random market data
      async (marketData) => {
        const market = await marketService.createMarket(marketData);
        
        // Initialize multiple times concurrently
        const results = await Promise.all([
          marketService.initializeMarket(market.id),
          marketService.initializeMarket(market.id),
          marketService.initializeMarket(market.id)
        ]);
        
        // All should return same onChainId
        const onChainIds = results.map(r => r.onChainId);
        expect(new Set(onChainIds).size).toBe(1);
      }
    ),
    { numRuns: 100 }
  );
});
```

**Property 6: Data Conversion Round-Trip**
```typescript
// Feature: hybrid-market-system, Property 6: Data Conversion Consistency
test('MOVE to octas round-trip preserves value', async () => {
  await fc.assert(
    fc.property(
      fc.double({ min: 0.00000001, max: 1000000 }), // random MOVE amount
      (moveAmount) => {
        const octas = contractService.moveToOctas(moveAmount);
        const backToMove = contractService.octasToMove(octas);
        expect(backToMove).toBeCloseTo(moveAmount, 8);
      }
    ),
    { numRuns: 100 }
  );
});
```

### Integration Tests

**End-to-End Market Creation Flow:**
1. Create user via wallet auth
2. Create market (off-chain)
3. Initialize market (on-chain)
4. Verify market exists on blockchain
5. Place vote
6. Verify vote recorded on-chain

**Concurrent Initialization Test:**
1. Create market
2. Trigger 10 concurrent initialization requests
3. Verify only one on-chain market created
4. Verify all requests return same onChainId

**Relay Wallet Monitoring:**
1. Mock low balance scenario
2. Attempt initialization
3. Verify error returned
4. Verify no transaction submitted

### Testing Framework

- **Unit Tests**: Jest
- **Property Tests**: fast-check
- **Integration Tests**: Supertest + Test Database
- **E2E Tests**: Playwright (for frontend integration)

### Test Configuration

```typescript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  setupFilesAfterEnv: ['<rootDir>/tests/setup.ts'],
  testMatch: ['**/*.test.ts'],
  collectCoverageFrom: [
    'src/**/*.ts',
    '!src/**/*.d.ts',
    '!src/index.ts'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};
```

## Security Considerations

### Relay Wallet Security

1. **Private Key Storage:**
   - Store in environment variables, never in code
   - Use secrets management service in production (AWS Secrets Manager, HashiCorp Vault)
   - Rotate keys periodically

2. **Access Control:**
   - Relay wallet only accessible by backend services
   - No direct API exposure of relay wallet operations
   - Admin endpoints protected by separate authentication

3. **Balance Monitoring:**
   - Alert when balance drops below threshold
   - Automatic top-up mechanism (optional)
   - Transaction logging for audit

### Authentication Security

1. **Nonce Management:**
   - One-time use nonces
   - Short TTL (5 minutes)
   - Stored in Redis for fast lookup

2. **JWT Security:**
   - Strong secret key (min 256 bits)
   - Short expiration (7 days)
   - Include wallet address in payload
   - Verify signature on every request

3. **Rate Limiting:**
   - Limit auth attempts per IP
   - Limit market creation per user
   - Limit initialization requests per market

### Smart Contract Interaction

1. **Transaction Validation:**
   - Validate all parameters before submission
   - Check gas estimates before sending
   - Verify transaction success before updating database

2. **Data Integrity:**
   - Verify on-chain data matches expectations
   - Handle chain reorganizations
   - Implement data reconciliation jobs

## Deployment Considerations

### Environment Variables

```bash
# Database
DATABASE_URL=postgresql://user:pass@host:5432/predictly

# Redis
REDIS_URL=redis://localhost:6379

# JWT
JWT_SECRET=<strong-secret-key>

# Relay Wallet
RELAY_WALLET_PRIVATE_KEY=<private-key>
RELAY_WALLET_MIN_BALANCE=10

# Movement Network
MOVEMENT_RPC_URL=https://testnet.movementnetwork.xyz/v1
CONTRACT_ADDRESS=0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565

# Monitoring
SENTRY_DSN=<sentry-dsn>
LOG_LEVEL=info
```

### Database Migrations

1. **Migration 1: Make privyId nullable**
```sql
ALTER TABLE "User" ALTER COLUMN "privyId" DROP NOT NULL;
```

2. **Migration 2: Add wallet address index**
```sql
CREATE UNIQUE INDEX "User_walletAddress_key" ON "User"("walletAddress");
```

3. **Migration 3: Add initialization lock table**
```sql
CREATE TABLE "InitializationLock" (
  "marketId" TEXT PRIMARY KEY,
  "lockedAt" TIMESTAMP NOT NULL DEFAULT NOW(),
  "lockedBy" TEXT NOT NULL,
  "expiresAt" TIMESTAMP NOT NULL
);
```

### Monitoring and Alerts

**Key Metrics:**
- Relay wallet balance
- Market initialization success rate
- Authentication success rate
- API response times
- On-chain transaction success rate

**Alerts:**
- Relay wallet balance < threshold
- High initialization failure rate (> 5%)
- High authentication failure rate (> 10%)
- Slow API responses (> 2s p95)

### Scaling Considerations

**Database:**
- Index on walletAddress for fast user lookup
- Index on groupId for fast market queries
- Index on status for filtering
- Consider read replicas for heavy read load

**Caching:**
- Cache on-chain data in Redis (TTL: 30s)
- Cache user data after authentication
- Cache group market lists

**Background Jobs:**
- Periodic sync of on-chain data (every 1 minute)
- Cleanup expired nonces (every 5 minutes)
- Monitor relay wallet balance (every 1 minute)
- Reconcile database with blockchain (every 1 hour)
