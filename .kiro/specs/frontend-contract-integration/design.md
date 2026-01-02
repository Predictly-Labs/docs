# Design Document: Frontend Contract Integration

## Overview

This design document describes the integration of the Predictly web frontend with the Movement Network smart contract. The integration enables users to interact with on-chain prediction markets through their Privy-connected wallets.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Frontend (Next.js)                       │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐  │
│  │   Pages/    │  │   Hooks     │  │      Providers          │  │
│  │ Components  │──│ useContract │──│  ContractProvider       │  │
│  └─────────────┘  │ useWallet   │  │  PrivyProvider          │  │
│                   └─────────────┘  └─────────────────────────┘  │
│                          │                    │                  │
│                          ▼                    ▼                  │
│  ┌─────────────────────────────────────────────────────────────┐│
│  │                  Contract Service (lib/contract.ts)         ││
│  │  - View functions (getMarketState, getMarketPools, etc.)    ││
│  │  - Payload builders (buildPlaceVotePayload, etc.)           ││
│  │  - Conversion utils (octasToMove, moveToOctas)              ││
│  └─────────────────────────────────────────────────────────────┘│
│                          │                                       │
└──────────────────────────┼───────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Movement Testnet (Bardock)                    │
│  RPC: https://testnet.movementnetwork.xyz/v1                    │
│  Contract: 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565 │
└─────────────────────────────────────────────────────────────────┘
```

## Components and Interfaces

### 1. Contract Service (`lib/contract.ts`)

Central service for all blockchain interactions.

```typescript
// Configuration
const CONTRACT_ADDRESS = process.env.NEXT_PUBLIC_CONTRACT_ADDRESS;
const RPC_URL = process.env.NEXT_PUBLIC_MOVEMENT_RPC_URL;

// Conversion utilities
function octasToMove(octas: bigint | string): number;
function moveToOctas(move: number): bigint;
function basisPointsToPercent(bp: bigint | string): number;

// View functions
async function getMarketCount(): Promise<number>;
async function getMarketState(marketId: number): Promise<MarketState>;
async function getMarketPools(marketId: number): Promise<{ yesPool: number; noPool: number }>;
async function getPercentages(marketId: number): Promise<{ yesPercent: number; noPercent: number }>;
async function getParticipantCount(marketId: number): Promise<number>;
async function getVoteInfo(marketId: number, voter: string): Promise<VoteInfo>;
async function calculateReward(marketId: number, voter: string): Promise<number>;

// Payload builders (for wallet signing)
function buildCreateMarketPayload(params: CreateMarketParams): TransactionPayload;
function buildPlaceVotePayload(params: PlaceVoteParams): TransactionPayload;
function buildResolvePayload(params: ResolveParams): TransactionPayload;
function buildClaimRewardPayload(marketId: number): TransactionPayload;
```

### 2. Contract Provider (`providers/ContractProvider.tsx`)

React context for contract state and methods.

```typescript
interface ContractContextValue {
  // State
  isConnected: boolean;
  walletAddress: string | null;
  
  // View functions
  getMarketState: (marketId: number) => Promise<MarketState>;
  getMarketPools: (marketId: number) => Promise<PoolData>;
  
  // Transaction functions
  createMarket: (params: CreateMarketParams) => Promise<TransactionResult>;
  placeVote: (params: PlaceVoteParams) => Promise<TransactionResult>;
  resolve: (params: ResolveParams) => Promise<TransactionResult>;
  claimReward: (marketId: number) => Promise<TransactionResult>;
  
  // Transaction state
  isPending: boolean;
  error: string | null;
}
```

### 3. useContract Hook (`hooks/useContract.ts`)

Hook for accessing contract functionality in components.

```typescript
function useContract() {
  const context = useContext(ContractContext);
  return context;
}
```

### 4. useWallet Hook (`hooks/useWallet.ts`)

Hook for wallet connection via Privy.

```typescript
function useWallet() {
  const { user, login, logout, authenticated } = usePrivy();
  
  return {
    isConnected: authenticated,
    address: user?.wallet?.address,
    connect: login,
    disconnect: logout,
  };
}
```

## Data Models

### MarketState

```typescript
interface MarketState {
  id: number;
  creator: string;
  title: string;
  description: string;
  endTime: number;
  minStake: number;      // in MOVE
  maxStake: number;      // in MOVE
  marketType: 0 | 1;     // 0=STANDARD, 1=NO_LOSS
  resolver: string;
  status: 0 | 1 | 2;     // 0=ACTIVE, 1=RESOLVED, 2=CANCELLED
  outcome: 0 | 1 | 2 | 3; // 0=PENDING, 1=YES, 2=NO, 3=INVALID
  yesPool: number;       // in MOVE
  noPool: number;        // in MOVE
  participantCount: number;
  createdAt: number;
  resolvedAt: number;
}
```

### VoteInfo

```typescript
interface VoteInfo {
  voter: string;
  prediction: 1 | 2;     // 1=YES, 2=NO
  amount: number;        // in MOVE
  timestamp: number;
  hasClaimed: boolean;
}
```

### Transaction Params

```typescript
interface CreateMarketParams {
  title: string;
  description: string;
  endTime: number;       // Unix timestamp
  minStake: number;      // in MOVE
  maxStake: number;      // in MOVE (0 = unlimited)
  resolver: string;
  marketType: 0 | 1;
}

interface PlaceVoteParams {
  marketId: number;
  prediction: 1 | 2;     // 1=YES, 2=NO
  amount: number;        // in MOVE
}

interface ResolveParams {
  marketId: number;
  outcome: 1 | 2 | 3;    // 1=YES, 2=NO, 3=INVALID
}
```

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system-essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Octas to MOVE conversion preserves value
*For any* valid octas value, converting to MOVE and back to octas should return the original value (within rounding tolerance).
**Validates: Requirements 2.2, 7.3**

### Property 2: MOVE to octas conversion preserves value
*For any* valid MOVE value, converting to octas should multiply by 100,000,000 exactly.
**Validates: Requirements 4.2, 7.3**

### Property 3: Basis points to percentage conversion
*For any* valid basis points value (0-10000), converting to percentage should divide by 100.
**Validates: Requirements 2.3**

### Property 4: Place vote payload structure
*For any* valid PlaceVoteParams, the built payload should contain the correct function name and all required arguments in the correct order.
**Validates: Requirements 3.1**

### Property 5: Create market payload structure
*For any* valid CreateMarketParams, the built payload should contain the correct function name and all required arguments with MOVE amounts converted to octas.
**Validates: Requirements 4.1**

### Property 6: Resolve payload structure
*For any* valid ResolveParams, the built payload should contain the correct function name and market_id and outcome arguments.
**Validates: Requirements 5.1**

### Property 7: Claim reward payload structure
*For any* valid market ID, the built payload should contain the correct function name and market_id argument.
**Validates: Requirements 6.1**

## Error Handling

### Transaction Errors

| Error Code | Description | User Message |
|------------|-------------|--------------|
| E_MARKET_NOT_ACTIVE | Market is not active | "This market is no longer accepting votes" |
| E_MARKET_ENDED | Market voting period ended | "Voting has ended for this market" |
| E_ALREADY_VOTED | User already voted | "You have already voted on this market" |
| E_STAKE_TOO_LOW | Stake below minimum | "Stake amount is below the minimum" |
| E_STAKE_TOO_HIGH | Stake above maximum | "Stake amount exceeds the maximum" |
| E_NOT_RESOLVER | Not authorized to resolve | "Only the resolver can resolve this market" |
| E_MARKET_NOT_ENDED | Market hasn't ended yet | "Market voting period hasn't ended yet" |
| E_NOT_WINNER | User is not a winner | "You did not win this prediction" |
| E_ALREADY_CLAIMED | Already claimed reward | "You have already claimed your reward" |

### Network Errors

- RPC connection failure → Show "Network error, please try again"
- Transaction timeout → Show "Transaction timed out, please check your wallet"
- Insufficient balance → Show "Insufficient MOVE balance"

## Testing Strategy

### Unit Tests

1. **Conversion functions**: Test octasToMove, moveToOctas, basisPointsToPercent with edge cases
2. **Payload builders**: Test each builder produces correct structure
3. **Error parsing**: Test error code to message mapping

### Property-Based Tests

Using fast-check library for property-based testing:

1. **Conversion round-trip**: octasToMove(moveToOctas(x)) ≈ x
2. **Payload structure**: All payloads have required fields
3. **Percentage bounds**: Converted percentages are 0-100

### Integration Tests

1. **View functions**: Mock RPC responses and verify parsing
2. **Transaction flow**: Mock wallet signing and verify payload submission
3. **Error handling**: Verify error messages for each error code

### E2E Tests

1. **Connect wallet flow**: Test Privy connection
2. **Vote flow**: Create market → Place vote → Verify pools updated
3. **Claim flow**: Resolve market → Claim reward → Verify balance
