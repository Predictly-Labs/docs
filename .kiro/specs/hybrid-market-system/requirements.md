# Requirements Document

## Introduction

This document specifies the requirements for implementing a hybrid prediction market system where market creation happens off-chain (backend) for free, but voting and money handling happens on-chain for trustless execution. The system uses lazy initialization where markets are only created on-chain when the first vote is placed, optimizing for cost efficiency while maintaining decentralization for financial operations.

## Glossary

- **Backend (BE)**: The Node.js/Express API server with PostgreSQL database
- **Smart Contract (SC)**: The Move smart contract deployed on Movement Network
- **Frontend (FE)**: The Next.js web application
- **Lazy Initialization**: Pattern where on-chain market creation is deferred until first vote
- **Relay Wallet**: Backend-controlled wallet that pays gas fees for market initialization
- **On-Chain ID**: The unique identifier for a market on the blockchain
- **Market Status**: PENDING (not on-chain), ACTIVE (on-chain), RESOLVED, CANCELLED
- **Wallet Signature**: Cryptographic signature proving wallet ownership for authentication
- **Octas**: Smallest unit of MOVE token (1 MOVE = 100,000,000 octas)

## Requirements

### Requirement 1: Wallet-Based Authentication

**User Story:** As a user, I want to authenticate using my Aptos/Movement wallet signature, so that I can access the platform without traditional login credentials.

#### Acceptance Criteria

1. WHEN a user connects their wallet THEN the system SHALL generate a sign-in message with nonce and timestamp
2. WHEN a user signs the message THEN the system SHALL verify the signature against the wallet address
3. WHEN signature verification succeeds THEN the system SHALL create or retrieve the user account and generate a JWT token
4. WHEN signature verification fails THEN the system SHALL return an authentication error
5. WHEN a JWT token is provided THEN the system SHALL validate it and attach user context to requests
6. THE system SHALL use wallet address as the primary user identifier instead of privyId

### Requirement 2: Off-Chain Market Creation

**User Story:** As a user, I want to create prediction markets without paying gas fees, so that I can easily create markets without upfront costs.

#### Acceptance Criteria

1. WHEN a user creates a market THEN the system SHALL save market metadata to the backend database
2. WHEN a market is created THEN the system SHALL set status to PENDING and onChainId to null
3. WHEN a market is created THEN the system SHALL validate required fields (title, description, endDate, groupId)
4. WHEN a market is created THEN the system SHALL associate it with the creator's wallet address
5. WHEN a market is created THEN the system SHALL return the market ID immediately without blockchain interaction

### Requirement 3: Lazy On-Chain Market Initialization

**User Story:** As a system, I want to initialize markets on-chain only when needed, so that we minimize gas costs and blockchain bloat.

#### Acceptance Criteria

1. WHEN a user attempts to vote on a PENDING market THEN the system SHALL initialize the market on-chain first
2. WHEN initializing a market THEN the backend SHALL use the relay wallet to create the market on the smart contract
3. WHEN market creation succeeds THEN the system SHALL save the onChainId and update status to ACTIVE
4. WHEN market creation fails THEN the system SHALL return an error and keep status as PENDING
5. WHEN a market is already ACTIVE THEN the system SHALL skip initialization and proceed with voting
6. THE system SHALL prevent concurrent initialization attempts for the same market

### Requirement 4: Relay Wallet Management

**User Story:** As a backend system, I want to manage a relay wallet securely, so that I can pay gas fees for market initialization on behalf of users.

#### Acceptance Criteria

1. THE system SHALL load relay wallet private key from environment variables
2. THE system SHALL monitor relay wallet balance and log warnings when balance is low
3. WHEN initializing a market THEN the system SHALL use the relay wallet to sign and submit the transaction
4. WHEN relay wallet balance is insufficient THEN the system SHALL return an error and prevent initialization
5. THE system SHALL log all relay wallet transactions for audit purposes

### Requirement 5: Market Initialization Endpoint

**User Story:** As a frontend developer, I want a dedicated endpoint to initialize markets, so that I can trigger on-chain creation before voting.

#### Acceptance Criteria

1. THE system SHALL provide a POST /api/markets/:id/initialize endpoint
2. WHEN the endpoint is called THEN the system SHALL verify the market exists and is PENDING
3. WHEN initialization succeeds THEN the system SHALL return the onChainId and transaction hash
4. WHEN the market is already initialized THEN the system SHALL return the existing onChainId
5. WHEN initialization fails THEN the system SHALL return appropriate error details
6. THE endpoint SHALL be protected by authentication middleware

### Requirement 6: On-Chain Data Synchronization

**User Story:** As a user, I want to see real-time market data from the blockchain, so that I can make informed voting decisions.

#### Acceptance Criteria

1. WHEN a market is ACTIVE THEN the system SHALL fetch on-chain data (pools, percentages, participant count)
2. WHEN fetching on-chain data THEN the system SHALL convert octas to MOVE for display
3. WHEN fetching on-chain data THEN the system SHALL convert basis points to percentages
4. WHEN on-chain data fetch fails THEN the system SHALL return cached backend data as fallback
5. THE system SHALL provide an endpoint to manually sync on-chain data to backend cache

### Requirement 7: Vote Flow Integration

**User Story:** As a user, I want to vote on markets seamlessly, so that the system handles initialization automatically if needed.

#### Acceptance Criteria

1. WHEN a user votes on a PENDING market THEN the frontend SHALL call the initialize endpoint first
2. WHEN initialization completes THEN the frontend SHALL proceed with the on-chain vote transaction
3. WHEN a user votes on an ACTIVE market THEN the frontend SHALL directly call the smart contract
4. WHEN a vote transaction succeeds THEN the system SHALL update backend statistics
5. WHEN a vote transaction fails THEN the system SHALL display the error to the user

### Requirement 8: Database Schema Migration

**User Story:** As a developer, I want to migrate the database schema from Privy-based to wallet-based authentication, so that the system supports the new authentication model.

#### Acceptance Criteria

1. THE system SHALL add a migration to make privyId nullable
2. THE system SHALL ensure walletAddress is unique and required for new users
3. THE system SHALL update user lookup queries to use walletAddress as primary identifier
4. THE system SHALL maintain backward compatibility with existing Privy users during transition
5. THE system SHALL provide a migration path for existing users to link their wallets

### Requirement 9: Error Handling and Retry Logic

**User Story:** As a system, I want robust error handling for blockchain operations, so that temporary failures don't break the user experience.

#### Acceptance Criteria

1. WHEN market initialization fails due to network error THEN the system SHALL retry up to 3 times
2. WHEN initialization fails permanently THEN the system SHALL log the error and return details to frontend
3. WHEN a race condition is detected THEN the system SHALL use database locks to prevent duplicate initialization
4. WHEN relay wallet runs out of gas THEN the system SHALL alert administrators
5. THE system SHALL provide clear error messages for different failure scenarios

### Requirement 10: Group and Market Association

**User Story:** As a user, I want to view all markets within a group, so that I can see relevant predictions for my communities.

#### Acceptance Criteria

1. WHEN querying group markets THEN the system SHALL return both PENDING and ACTIVE markets
2. WHEN displaying markets THEN the system SHALL indicate which markets are on-chain vs off-chain
3. WHEN a market is PENDING THEN the system SHALL show "Not yet on-chain" status
4. WHEN a market is ACTIVE THEN the system SHALL fetch and display on-chain statistics
5. THE system SHALL allow filtering markets by status (PENDING, ACTIVE, RESOLVED)

### Requirement 11: Market Parameter Conversion

**User Story:** As a system, I want to correctly convert market parameters between backend and smart contract formats, so that data consistency is maintained.

#### Acceptance Criteria

1. WHEN initializing a market THEN the system SHALL convert endDate to Unix timestamp
2. WHEN initializing a market THEN the system SHALL convert minStake from MOVE to octas
3. WHEN initializing a market THEN the system SHALL map marketType enum correctly
4. WHEN fetching on-chain data THEN the system SHALL convert octas back to MOVE
5. WHEN fetching on-chain data THEN the system SHALL convert basis points back to percentages

### Requirement 12: Security and Access Control

**User Story:** As a system administrator, I want secure handling of sensitive operations, so that the platform is protected from unauthorized access.

#### Acceptance Criteria

1. THE relay wallet private key SHALL be stored in environment variables, never in code
2. THE system SHALL validate JWT tokens on all protected endpoints
3. WHEN a user creates a market THEN the system SHALL verify they are authenticated
4. WHEN a user initializes a market THEN the system SHALL verify they have permission
5. THE system SHALL rate-limit market creation to prevent spam
