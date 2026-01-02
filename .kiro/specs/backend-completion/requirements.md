# Requirements Document

## Introduction

This document specifies the requirements for completing the Predictly backend API. The platform is a social prediction market for friend groups running on Movement Network. The backend needs additional endpoints for prediction market management, voting system, market resolution by judges, subscription/pro features, and mock yield rewards for hackathon demonstration.

## Glossary

- **Prediction Market**: A market where users can bet YES or NO on a future outcome
- **Vote**: A user's prediction (YES/NO) with staked amount on a market
- **Judge**: A group member with JUDGE role who can resolve markets
- **Resolution**: The act of determining the final outcome of a market
- **Yield**: Mock rewards generated from staked amounts (for demo purposes)
- **Pro User**: A user with active subscription for premium features
- **Pool**: Total staked amount in a market (yesPool + noPool)

## Requirements

### Requirement 1

**User Story:** As a group member, I want to create prediction markets within my group, so that I can start predictions with my friends.

#### Acceptance Criteria

1. WHEN a group member submits a valid market creation request THEN the System SHALL create a new prediction market with ACTIVE status and store it in the database
2. WHEN a user attempts to create a market without being a group member THEN the System SHALL reject the request with a 403 forbidden error
3. WHEN a market is created THEN the System SHALL set initial yesPercentage and noPercentage to 50
4. WHEN a market is created with an image THEN the System SHALL store the IPFS URL from Pinata in the imageUrl field
5. WHEN a market creation request has invalid data THEN the System SHALL return validation errors with specific field messages

### Requirement 2

**User Story:** As a user, I want to view prediction markets, so that I can decide which ones to participate in.

#### Acceptance Criteria

1. WHEN a user requests markets for a group THEN the System SHALL return paginated markets with stats (totalVolume, participantCount, yesPercentage, noPercentage)
2. WHEN a user requests a specific market THEN the System SHALL return full market details including votes and creator info
3. WHEN a user requests markets with status filter THEN the System SHALL return only markets matching that status
4. WHEN a user requests markets sorted by volume THEN the System SHALL return markets ordered by totalVolume descending

### Requirement 3

**User Story:** As a group member, I want to place votes (YES/NO) on prediction markets, so that I can participate and potentially win rewards.

#### Acceptance Criteria

1. WHEN a user places a vote on an ACTIVE market THEN the System SHALL create a vote record and update market pools and percentages
2. WHEN a user attempts to vote on a non-ACTIVE market THEN the System SHALL reject with appropriate error message
3. WHEN a user attempts to vote twice on the same market THEN the System SHALL reject with "already voted" error
4. WHEN a vote is placed THEN the System SHALL recalculate yesPercentage and noPercentage based on pool ratios
5. WHEN a vote amount is below minStake THEN the System SHALL reject with validation error
6. WHEN a vote amount exceeds maxStake (if set) THEN the System SHALL reject with validation error

### Requirement 4

**User Story:** As a judge, I want to resolve prediction markets after the deadline, so that winners can claim their rewards.

#### Acceptance Criteria

1. WHEN a judge resolves a market THEN the System SHALL update status to RESOLVED and set the outcome (YES/NO/INVALID)
2. WHEN a non-judge attempts to resolve a market THEN the System SHALL reject with 403 forbidden error
3. WHEN a judge attempts to resolve a market before endDate THEN the System SHALL reject with "market not ended" error
4. WHEN a market is resolved THEN the System SHALL calculate and store reward amounts for winning voters
5. WHEN a market is resolved as INVALID THEN the System SHALL mark all votes for refund (original amount as reward)

### Requirement 5

**User Story:** As a winning voter, I want to claim my rewards after market resolution, so that I can receive my earnings.

#### Acceptance Criteria

1. WHEN a winning voter claims reward THEN the System SHALL mark hasClaimedReward as true and return the reward amount
2. WHEN a user attempts to claim reward on unresolved market THEN the System SHALL reject with appropriate error
3. WHEN a user attempts to claim reward twice THEN the System SHALL reject with "already claimed" error
4. WHEN a losing voter attempts to claim reward THEN the System SHALL reject with "not eligible" error

### Requirement 6

**User Story:** As a user, I want to subscribe to Pro features, so that I can access premium functionality.

#### Acceptance Criteria

1. WHEN a user initiates subscription THEN the System SHALL create a checkout session and return payment URL
2. WHEN payment is successful THEN the System SHALL update user isPro to true and set proExpiresAt
3. WHEN a user checks subscription status THEN the System SHALL return current pro status and expiration date
4. WHEN pro subscription expires THEN the System SHALL treat user as non-pro for feature access

### Requirement 7

**User Story:** As a platform user, I want to see mock yield rewards on my predictions, so that I can understand the no-loss market concept.

#### Acceptance Criteria

1. WHEN a user views their votes THEN the System SHALL display mock yield earned based on stake amount and duration
2. WHEN calculating mock yield THEN the System SHALL use a fixed APY rate (e.g., 5%) prorated by days staked
3. WHEN a market is NO_LOSS type THEN the System SHALL calculate rewards from yield only, not from losing stakes

### Requirement 8

**User Story:** As a user, I want to see my prediction history and stats, so that I can track my performance.

#### Acceptance Criteria

1. WHEN a user requests their votes THEN the System SHALL return paginated vote history with market details
2. WHEN a user requests their stats THEN the System SHALL return totalPredictions, correctPredictions, winRate, totalEarnings, currentStreak
3. WHEN a market is resolved THEN the System SHALL update user stats (increment totalPredictions, correctPredictions if won)
