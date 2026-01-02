# Requirements Document

## Introduction

This document defines the requirements for integrating the Predictly frontend (Next.js) with the backend API. The integration will enable users to authenticate, manage groups, create and participate in prediction markets, and track their rewards through a seamless user interface.

## Glossary

- **Frontend**: Next.js web application at `/web`
- **Backend**: Express.js API server at `/backend`
- **Privy**: Third-party authentication provider for Web3 wallets
- **JWT**: JSON Web Token used for API authentication
- **API Client**: Centralized service for making HTTP requests to backend

## Requirements

### Requirement 1: API Client Setup

**User Story:** As a developer, I want a centralized API client, so that all backend requests are handled consistently with proper error handling and authentication.

#### Acceptance Criteria

1. THE Frontend SHALL have a centralized API client that handles all HTTP requests to the backend
2. WHEN making authenticated requests THEN the API client SHALL automatically include the JWT token in the Authorization header
3. WHEN a request fails with 401 status THEN the API client SHALL trigger a logout and redirect to login
4. THE API client SHALL use environment variables for the backend URL configuration

### Requirement 2: Authentication Integration

**User Story:** As a user, I want to login with my wallet via Privy, so that I can access the prediction market features.

#### Acceptance Criteria

1. WHEN a user authenticates with Privy THEN the Frontend SHALL send the privyId to the backend auth endpoint
2. WHEN the backend returns a JWT token THEN the Frontend SHALL store it securely for subsequent requests
3. WHEN a user logs out THEN the Frontend SHALL clear the stored token and user data
4. THE Frontend SHALL provide an AuthContext that exposes user state and auth methods to all components

### Requirement 3: Groups Integration

**User Story:** As a user, I want to view, create, and join prediction groups, so that I can participate in predictions with friends.

#### Acceptance Criteria

1. WHEN a user visits the groups page THEN the Frontend SHALL fetch and display the list of public groups
2. WHEN a user creates a group THEN the Frontend SHALL send the group data to the backend and update the UI
3. WHEN a user joins a group with an invite code THEN the Frontend SHALL validate and process the join request
4. WHEN viewing a group THEN the Frontend SHALL display group details, members, and prediction markets

### Requirement 4: Predictions Integration

**User Story:** As a user, I want to create predictions and vote on them, so that I can participate in the prediction market.

#### Acceptance Criteria

1. WHEN a user views a group THEN the Frontend SHALL fetch and display all prediction markets in that group
2. WHEN a user creates a prediction market THEN the Frontend SHALL send the market data to the backend
3. WHEN a user places a vote THEN the Frontend SHALL send the vote data and update the market percentages
4. WHEN viewing a market THEN the Frontend SHALL display current YES/NO percentages and participant count

### Requirement 5: Rewards Integration

**User Story:** As a user, I want to view my voting history and claim rewards, so that I can track my earnings.

#### Acceptance Criteria

1. WHEN a user visits the rewards page THEN the Frontend SHALL fetch and display their vote history
2. WHEN a market is resolved THEN the Frontend SHALL show the user's reward amount if eligible
3. WHEN a user claims a reward THEN the Frontend SHALL process the claim and update the UI
4. THE Frontend SHALL display mock yield earnings for each active vote

### Requirement 6: User Profile Integration

**User Story:** As a user, I want to view and update my profile, so that I can manage my account information.

#### Acceptance Criteria

1. WHEN a user is authenticated THEN the Frontend SHALL fetch and display their profile data
2. WHEN a user updates their profile THEN the Frontend SHALL send the updated data to the backend
3. THE Frontend SHALL display user statistics including total predictions, accuracy, and earnings
