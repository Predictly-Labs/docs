# Implementation Plan: Missing API Features

## Overview

This implementation plan covers the development of missing API endpoints for the Predictly backend, organized into three sprints based on priority.

## Tasks

### Sprint 1: Critical Features (Week 1)

- [x] 1. Implement My Groups Endpoint
  - [x] 1.1 Add route in `groups.routes.ts`
    - Add `GET /my-groups` route with auth middleware
    - Wire to `getMyGroups` controller
    - _Requirements: 1.1, 1.5_
  
  - [x] 1.2 Create validator schema in `groups.validator.ts`
    - Define `myGroupsQuerySchema` with Zod
    - Validate page, limit, role, search, sort parameters
    - _Requirements: 1.2, 1.3, 7.4_
  
  - [x] 1.3 Implement controller in `groups.controller.ts`
    - Query `GroupMember` with user ID filter
    - Apply role and search filters
    - Include group stats (_count, markets)
    - Implement pagination logic
    - Calculate total volume from active markets
    - _Requirements: 1.1, 1.2, 1.3, 1.4, 1.5_
  
  - [ ]* 1.4 Write property test for My Groups completeness
    - **Property 1: My Groups Completeness**
    - **Validates: Requirements 1.1**
  
  - [ ]* 1.5 Write unit tests for My Groups endpoint
    - Test pagination
    - Test role filtering
    - Test search functionality
    - Test sorting options
    - _Requirements: 1.1, 1.2, 1.3, 1.5_

- [x] 2. Enhance My Votes Endpoint with Pagination
  - [x] 2.1 Update validator in `predictions.validator.ts`
    - Create `myVotesQuerySchema` with pagination
    - Add status, groupId, outcome filters
    - _Requirements: 2.1, 2.2, 2.3, 2.4_
  
  - [x] 2.2 Update controller in `predictions.controller.ts`
    - Modify `getUserVotes` to accept query parameters
    - Implement pagination with skip/take
    - Add status filter (ACTIVE, RESOLVED, PENDING)
    - Add groupId filter
    - Add outcome filter (won, lost, pending)
    - Calculate total count for pagination
    - _Requirements: 2.1, 2.2, 2.3, 2.4_
  
  - [ ]* 2.3 Write property test for pagination consistency
    - **Property 2: Pagination Consistency**
    - **Validates: Requirements 2.1, 7.1**
  
  - [ ]* 2.4 Write unit tests for My Votes filters
    - Test status filtering
    - Test group filtering
    - Test outcome filtering
    - Test pagination
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

- [x] 3. Implement Check My Vote Endpoint
  - [x] 3.1 Add route in `predictions.routes.ts`
    - Add `GET /:marketId/my-vote` route
    - Apply auth middleware
    - _Requirements: 2.5_
  
  - [x] 3.2 Implement controller in `predictions.controller.ts`
    - Create `getMyVoteOnMarket` function
    - Query vote with unique constraint (marketId, userId)
    - Return vote or null if not found
    - Include market details
    - _Requirements: 2.5_
  
  - [ ]* 3.3 Write property test for vote uniqueness
    - **Property 4: Vote Uniqueness**
    - **Validates: Requirements 2.5**
  
  - [ ]* 3.4 Write unit tests for Check My Vote
    - Test when vote exists
    - Test when vote doesn't exist
    - Test with invalid market ID
    - _Requirements: 2.5_

- [x] 4. Checkpoint - Sprint 1 Complete
  - Ensure all tests pass
  - Test endpoints manually with Postman
  - Update API documentation
  - Ask user if questions arise

### Sprint 2: High Priority Features (Week 2)

- [x] 5. Add Role Filter to Group Members
  - [x] 5.1 Update validator in `groups.validator.ts`
    - Add optional `role` query parameter to existing schema
    - _Requirements: 4.1_
  
  - [x] 5.2 Update controller in `groups.controller.ts`
    - Modify `getGroupMembers` to accept role filter
    - Apply role filter in Prisma query
    - _Requirements: 4.1_
  
  - [ ]* 5.3 Write property test for role filter correctness
    - **Property 3: Role Filter Correctness**
    - **Validates: Requirements 4.1**
  
  - [ ]* 5.4 Write unit tests for role filtering
    - Test filtering by each role type
    - Test without filter (all members)
    - _Requirements: 4.1_

- [x] 6. Implement My Votes Statistics Endpoint
  - [x] 6.1 Add route in `predictions.routes.ts`
    - Add `GET /my-votes/stats` route
    - Apply auth middleware
    - _Requirements: 3.1, 3.2, 3.3_
  
  - [x] 6.2 Implement controller in `predictions.controller.ts`
    - Create `getMyVotesStats` function
    - Fetch all user votes with market details
    - Calculate total votes, invested, earnings
    - Calculate ROI and win rate
    - Group statistics by group
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5_
  
  - [ ]* 6.3 Write property tests for statistics calculations
    - **Property 5: Statistics Accuracy**
    - **Property 6: Win Rate Calculation**
    - **Property 7: ROI Calculation**
    - **Validates: Requirements 3.1, 3.2**
  
  - [ ]* 6.4 Write unit tests for statistics endpoint
    - Test with no votes
    - Test with only active votes
    - Test with resolved votes (won/lost)
    - Test ROI calculation
    - Test win rate calculation
    - Test grouping by group
    - _Requirements: 3.1, 3.2, 3.3_

- [x] 7. Add Market Type Filter to Markets Endpoint
  - [x] 7.1 Update validator in `markets.routes.ts`
    - Add `marketType` query parameter to schema
    - Validate enum values (STANDARD, NO_LOSS, WITH_YIELD)
    - _Requirements: 5.1_
  
  - [x] 7.2 Update controller in `markets.controller.ts`
    - Modify `getMarkets` to accept marketType filter
    - Apply filter in Prisma where clause
    - _Requirements: 5.1_
  
  - [ ]* 7.3 Write property test for market type filter
    - **Property 9: Market Type Filter**
    - **Validates: Requirements 5.1**
  
  - [ ]* 7.4 Write unit tests for market type filtering
    - Test each market type
    - Test without filter
    - _Requirements: 5.1_

- [x] 8. Checkpoint - Sprint 2 Complete
  - Ensure all tests pass
  - Test new endpoints with Postman
  - Update API documentation
  - Ask user if questions arise

### Sprint 3: Polish Features (Week 3)

- [x] 9. Add Group Settings to Database
  - [x] 9.1 Update Prisma schema
    - Add `defaultMarketType` field to Group model
    - Add `allowedMarketTypes` array field to Group model
    - Set default values
    - _Requirements: 6.1, 6.2, 6.3_
  
  - [x] 9.2 Create and run migration
    - Generate migration with `prisma migrate dev`
    - Review migration SQL
    - Apply to development database
    - _Requirements: 6.1_
  
  - [x] 9.3 Update TypeScript types
    - Regenerate Prisma client
    - Verify types in IDE
    - _Requirements: 6.1_

- [x] 10. Implement Group Settings Endpoints
  - [x] 10.1 Add routes in `groups.routes.ts`
    - Add `GET /:id/settings` route
    - Add `PUT /:id/settings` route with auth middleware
    - _Requirements: 6.1, 6.4_
  
  - [x] 10.2 Create validator in `groups.validator.ts`
    - Define `groupSettingsSchema` for PUT request
    - Validate defaultMarketType and allowedMarketTypes
    - _Requirements: 6.2, 6.3_
  
  - [x] 10.3 Implement GET settings controller
    - Create `getGroupSettings` function
    - Query group settings fields
    - Return settings object
    - _Requirements: 6.1, 6.5_
  
  - [x] 10.4 Implement PUT settings controller
    - Create `updateGroupSettings` function
    - Check user is admin of group
    - Update settings in database
    - _Requirements: 6.1, 6.2, 6.3, 6.4_
  
  - [ ]* 10.5 Write property tests for settings
    - **Property 10: Settings Persistence**
    - **Property 11: Admin Authorization**
    - **Validates: Requirements 6.1, 6.4, 6.5**
  
  - [ ]* 10.6 Write unit tests for settings endpoints
    - Test GET settings
    - Test PUT settings as admin
    - Test PUT settings as non-admin (should fail)
    - Test invalid market types
    - _Requirements: 6.1, 6.2, 6.3, 6.4, 6.5_

- [x] 11. Implement Judge Resolution History
  - [x] 11.1 Add route in `users.routes.ts` or `predictions.routes.ts`
    - Add `GET /users/:userId/resolved-markets` route
    - _Requirements: 4.4_
  
  - [x] 11.2 Implement controller
    - Create `getResolvedMarkets` function
    - Query markets where resolvedById matches userId
    - Add pagination
    - Include group details
    - _Requirements: 4.4_
  
  - [ ]* 11.3 Write unit tests for judge history
    - Test with markets resolved by judge
    - Test with no resolved markets
    - Test pagination
    - _Requirements: 4.4_

- [x] 12. Implement Bulk Judge Assignment
  - [x] 12.1 Add route in `groups.routes.ts`
    - Add `POST /:groupId/judges/bulk` route
    - Apply auth middleware
    - _Requirements: 4.2_
  
  - [x] 12.2 Create validator
    - Define schema for array of user IDs
    - Validate UUID format
    - _Requirements: 4.2_
  
  - [x] 12.3 Implement controller
    - Create `bulkAssignJudges` function
    - Check requester is admin
    - Update multiple GroupMember records in transaction
    - Return success/failure for each user
    - _Requirements: 4.2_
  
  - [ ]* 12.4 Write unit tests for bulk assignment
    - Test successful bulk assignment
    - Test partial failures
    - Test as non-admin (should fail)
    - _Requirements: 4.2_

- [x] 13. Add Database Indexes for Performance
  - [x] 13.1 Create migration for indexes
    - Add index on GroupMember(userId, role)
    - Add index on Vote(userId, marketId)
    - Add index on PredictionMarket(marketType, status)
    - Add index on GroupMember(userId, joinedAt)
    - _Requirements: 8.2_
  
  - [x] 13.2 Test query performance
    - Measure query times before and after indexes
    - Verify improvement in response times
    - _Requirements: 8.1, 8.4, 8.5_

- [x] 14. Final Checkpoint - Sprint 3 Complete
  - Ensure all tests pass
  - Test all new endpoints
  - Update complete API documentation
  - Update Swagger docs
  - Update Postman collection
  - Deploy to staging
  - Ask user for final review

## Notes

- Tasks marked with `*` are optional test tasks
- Each sprint should be completed before moving to the next
- Database migrations in Sprint 3 require careful testing
- All endpoints should follow existing patterns for consistency
- Property tests should run with minimum 100 iterations

