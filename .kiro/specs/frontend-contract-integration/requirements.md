# Requirements Document

## Introduction

This document specifies the requirements for integrating the Predictly web frontend with the Movement Network smart contract. The integration enables users to interact directly with on-chain prediction markets through their wallets, including creating markets, placing votes, resolving markets, and claiming rewards.

## Glossary

- **Movement Network**: An Aptos-compatible L2 blockchain where Predictly smart contracts are deployed
- **Movement Testnet (Bardock)**: The test network for Movement with RPC URL `https://testnet.movementnetwork.xyz/v1`
- **Privy**: Authentication provider that handles wallet connections and embedded wallets
- **On-chain**: Data and operations stored/executed on the blockchain
- **Transaction**: A signed operation that modifies blockchain state
- **View Function**: A read-only blockchain query that doesn't require signing
- **Octas**: The smallest unit of MOVE token (1 MOVE = 100,000,000 octas)
- **Contract Address**: `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565`

## Requirements

### Requirement 1

**User Story:** As a user, I want to connect my wallet via Privy to the app, so that I can interact with on-chain prediction markets.

#### Acceptance Criteria

1. WHEN a user clicks the connect wallet button THEN the system SHALL use Privy to initiate wallet connection
2. WHEN Privy authentication succeeds THEN the system SHALL retrieve the user's wallet address
3. WHEN wallet connection succeeds THEN the system SHALL display the connected wallet address
4. WHEN wallet connection fails THEN the system SHALL display an appropriate error message
5. WHEN a user logs out via Privy THEN the system SHALL clear the wallet state and update the UI

### Requirement 2

**User Story:** As a user, I want to view on-chain market data, so that I can see real-time prediction market information.

#### Acceptance Criteria

1. WHEN a user views a market THEN the system SHALL fetch and display on-chain data (pools, percentages, participant count)
2. WHEN on-chain data is fetched THEN the system SHALL convert octas to MOVE for display (divide by 100,000,000)
3. WHEN on-chain data is fetched THEN the system SHALL convert basis points to percentages for display (divide by 100)
4. WHEN fetching on-chain data fails THEN the system SHALL display cached/backend data as fallback

### Requirement 3

**User Story:** As a user, I want to place votes on prediction markets using my Privy wallet, so that I can participate in predictions with real stakes.

#### Acceptance Criteria

1. WHEN a user submits a vote THEN the system SHALL build the place_vote transaction payload
2. WHEN a transaction is built THEN the system SHALL use Privy's wallet provider to sign the transaction
3. WHEN the user signs the transaction THEN the system SHALL submit it to the blockchain
4. WHEN the transaction succeeds THEN the system SHALL update the UI with new market data
5. WHEN the transaction fails THEN the system SHALL display the error reason to the user
6. WHILE a transaction is pending THEN the system SHALL display a loading state

### Requirement 4

**User Story:** As a market creator, I want to create prediction markets on-chain, so that the market data is stored permanently on the blockchain.

#### Acceptance Criteria

1. WHEN a user submits a new market THEN the system SHALL build the create_market transaction payload
2. WHEN creating a market THEN the system SHALL convert MOVE amounts to octas for the transaction
3. WHEN the transaction succeeds THEN the system SHALL retrieve the new market ID from the event
4. WHEN the transaction fails THEN the system SHALL display the error reason to the user

### Requirement 5

**User Story:** As a resolver, I want to resolve markets on-chain, so that the outcome is permanently recorded.

#### Acceptance Criteria

1. WHEN a resolver submits a resolution THEN the system SHALL build the resolve transaction payload
2. WHEN the transaction succeeds THEN the system SHALL update the market status to RESOLVED
3. WHEN a non-resolver attempts to resolve THEN the system SHALL display an authorization error

### Requirement 6

**User Story:** As a winner, I want to claim my rewards on-chain, so that I receive my winnings in my wallet.

#### Acceptance Criteria

1. WHEN a user claims rewards THEN the system SHALL build the claim_reward transaction payload
2. WHEN the transaction succeeds THEN the system SHALL display the claimed amount
3. WHEN a user has already claimed THEN the system SHALL prevent duplicate claims
4. WHEN a user is not a winner THEN the system SHALL display an appropriate message

### Requirement 7

**User Story:** As a developer, I want a clean contract service abstraction, so that blockchain interactions are centralized and maintainable.

#### Acceptance Criteria

1. THE contract service SHALL provide typed functions for all view operations
2. THE contract service SHALL provide typed functions for building all transaction payloads
3. THE contract service SHALL handle octas/MOVE conversion internally
4. THE contract service SHALL use environment variables for contract address and RPC URL
5. THE contract service SHALL connect to Movement Testnet (Bardock) by default
