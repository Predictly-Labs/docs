# Requirements Document

## Introduction

This specification covers the missing API endpoints and features identified during the brainstorming session for the Predictly backend. These features are essential for providing a complete user experience and enabling proper group management, user predictions tracking, and judge administration.

## Glossary

- **System**: The Predictly Backend API
- **User**: An authenticated user with a wallet address
- **Group**: A collection of users who can create and participate in prediction markets
- **Judge**: A user with the JUDGE role who can resolve prediction markets
- **Admin**: A user with the ADMIN role who has full control over a group
- **Market**: A prediction market where users can vote YES or NO
- **Vote**: A user's prediction on a specific market
- **Market_Type**: The type of market (STANDARD, NO_LOSS, or WITH_YIELD)

## Requirements

### Requirement 1: My Groups Management

**User Story:** As a user, I want to see all groups I am a member of, so that I can easily navigate to my groups without scrolling through all public groups.

#### Acceptance Criteria

1. WHEN a user requests their groups, THE System SHALL return all groups where the user is a member
2. WHEN a user filters by role, THE System SHALL return only groups where the user has that specific role
3. WHEN a user searches within their groups, THE System SHALL return groups matching the search term
4. WHEN a user requests their groups, THE System SHALL include membership statistics for each group
5. THE System SHALL paginate the results with configurable page size

### Requirement 2: User Predictions Tracking

**User Story:** As a user, I want to view my prediction history with filtering and pagination, so that I can track my performance and find specific predictions easily.

#### Acceptance Criteria

1. WHEN a user requests their votes, THE System SHALL return paginated results
2. WHEN a user filters by market status, THE System SHALL return only votes for markets with that status
3. WHEN a user filters by group, THE System SHALL return only votes for markets in that group
4. WHEN a user filters by outcome, THE System SHALL return only votes that won, lost, or are pending
5. WHEN a user requests a specific market vote, THE System SHALL return their vote for that market or null if not voted

### Requirement 3: User Statistics

**User Story:** As a user, I want to see aggregate statistics about my predictions, so that I can understand my performance across all groups.

#### Acceptance Criteria

1. WHEN a user requests their statistics, THE System SHALL calculate total votes, invested amount, and earnings
2. WHEN a user requests their statistics, THE System SHALL calculate win rate and ROI
3. WHEN a user requests their statistics, THE System SHALL provide statistics grouped by group
4. THE System SHALL include both active and resolved votes in statistics
5. THE System SHALL calculate mock yield for votes in WITH_YIELD markets

### Requirement 4: Judge Management

**User Story:** As a group admin, I want to manage judges in my group, so that I can control who can resolve prediction markets.

#### Acceptance Criteria

1. WHEN an admin requests group members filtered by role, THE System SHALL return only members with that role
2. WHEN an admin assigns multiple judges, THE System SHALL update all user roles in a single transaction
3. WHEN a judge resolves markets, THE System SHALL track resolution history
4. WHEN requesting a judge's history, THE System SHALL return all markets resolved by that judge
5. THE System SHALL prevent non-judges from resolving markets

### Requirement 5: Market Type Management

**User Story:** As a user, I want to filter markets by type, so that I can find zero-loss or standard markets easily.

#### Acceptance Criteria

1. WHEN a user filters markets by type, THE System SHALL return only markets of that type
2. WHEN a group has a default market type, THE System SHALL use it for new markets
3. WHEN a group restricts market types, THE System SHALL only allow creation of allowed types
4. THE System SHALL support STANDARD, NO_LOSS, and WITH_YIELD market types
5. THE System SHALL persist group market type settings

### Requirement 6: Group Settings

**User Story:** As a group admin, I want to configure group-level settings, so that I can customize the group's behavior.

#### Acceptance Criteria

1. WHEN an admin updates group settings, THE System SHALL persist the changes
2. WHEN an admin sets a default market type, THE System SHALL apply it to new markets
3. WHEN an admin restricts market types, THE System SHALL validate market creation
4. THE System SHALL only allow admins to modify group settings
5. THE System SHALL return current settings when requested

### Requirement 7: Data Consistency

**User Story:** As a developer, I want all endpoints to follow consistent patterns, so that the API is predictable and easy to use.

#### Acceptance Criteria

1. THE System SHALL use consistent pagination parameters across all list endpoints
2. THE System SHALL return consistent response formats with success, data, and pagination fields
3. THE System SHALL use consistent error response formats
4. THE System SHALL validate all query parameters using Zod schemas
5. THE System SHALL include proper HTTP status codes for all responses

### Requirement 8: Performance

**User Story:** As a user, I want fast API responses, so that the application feels responsive.

#### Acceptance Criteria

1. WHEN a user requests paginated data, THE System SHALL limit results to prevent performance issues
2. THE System SHALL use database indexes for frequently queried fields
3. THE System SHALL cache aggregate statistics for 5 minutes
4. THE System SHALL use efficient database queries with proper joins
5. THE System SHALL respond to list endpoints within 500ms under normal load

