# Implementation Plan: Hybrid Market System

## Overview

This implementation plan guides the development of a hybrid prediction market system where markets are created off-chain for free, but voting happens on-chain for trustless execution. The plan follows an incremental approach, building and testing each component before integration.

## Tasks

- [x] 1. Database Schema Migration
  - Update User model to support wallet-based authentication
  - Make privyId nullable for backward compatibility
  - Ensure walletAddress is unique and indexed
  - Add InitializationLock table for concurrency control
  - _Requirements: 8.1, 8.2, 8.3_

- [ ]* 1.1 Write unit tests for schema migration
  - Test that existing Privy users are preserved
  - Test that new users can be created with walletAddress only
  - Test unique constraint on walletAddress
  - _Requirements: 8.4_

- [x] 2. Implement Wallet Authentication Service
  - [x] 2.1 Create authentication service module
    - Implement generateSignInMessage function
    - Implement verifySignature function using @aptos-labs/ts-sdk
    - Implement JWT token generation and validation
    - Store nonces in Redis with TTL
    - _Requirements: 1.1, 1.2, 1.3, 1.5_

  - [ ]* 2.2 Write property test for signature verification
    - **Property 1: Signature Verification Correctness**
    - **Validates: Requirements 1.2, 1.3**

  - [ ]* 2.3 Write property test for token validity
    - **Property 7: Authentication Token Validity**
    - **Validates: Requirements 1.5**

  - [x] 2.4 Create authentication endpoints
    - POST /api/auth/wallet/message
    - POST /api/auth/wallet/verify
    - GET /api/auth/me
    - _Requirements: 1.1, 1.2, 1.3_

  - [ ]* 2.5 Write integration tests for auth flow
    - Test complete authentication flow
    - Test invalid signature rejection
    - Test expired nonce handling
    - _Requirements: 1.4_

- [x] 3. Update Authentication Middleware
  - [x] 3.1 Modify auth middleware to support wallet-based auth
    - Update JWT payload structure
    - Update user lookup to use walletAddress
    - Maintain backward compatibility with Privy tokens (temporary)
    - _Requirements: 1.5, 8.4_

  - [ ]* 3.2 Write unit tests for middleware
    - Test valid token acceptance
    - Test invalid token rejection
    - Test missing token handling
    - _Requirements: 1.5_

- [x] 4. Implement Relay Wallet Service
  - [x] 4.1 Create relay wallet service module
    - Load private key from environment variable
    - Implement getBalance function
    - Implement hasSufficientBalance check
    - Add balance monitoring and logging
    - _Requirements: 4.1, 4.2, 4.4_

  - [ ]* 4.2 Write property test for balance sufficiency
    - **Property 5: Relay Wallet Balance Sufficiency**
    - **Validates: Requirements 4.4**

  - [x] 4.3 Implement createMarketOnChain function
    - Build create_market transaction payload
    - Sign transaction with relay wallet
    - Submit transaction to Movement Network
    - Wait for confirmation and extract onChainId from events
    - Implement retry logic for transient failures
    - _Requirements: 4.3, 9.1_

  - [ ]* 4.4 Write unit tests for relay wallet
    - Test balance checking
    - Test transaction building
    - Test error handling for insufficient balance
    - Test retry logic
    - _Requirements: 4.4, 4.5, 9.1_

- [ ] 5. Checkpoint - Ensure authentication and relay wallet tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [x] 6. Implement Contract Service
  - [x] 6.1 Create contract service module
    - Implement getMarketData view function
    - Implement getUserVote view function
    - Implement data conversion utilities (MOVE ↔ octas, percentage ↔ basis points)
    - _Requirements: 6.1, 6.2, 6.3, 11.1, 11.2, 11.4, 11.5_

  - [ ]* 6.2 Write property test for data conversion
    - **Property 6: Data Conversion Consistency**
    - **Validates: Requirements 11.1, 11.2, 11.4**

  - [x] 6.3 Implement transaction payload builders
    - buildPlaceVotePayload
    - buildResolveMarketPayload
    - buildClaimRewardPayload
    - _Requirements: 11.1, 11.3_

  - [ ]* 6.4 Write unit tests for contract service
    - Test view function calls
    - Test payload building
    - Test data conversion edge cases
    - _Requirements: 6.1, 6.2, 6.3_

- [x] 7. Implement Market Service
  - [x] 7.1 Create market service module
    - Implement createMarket function (off-chain)
    - Implement getMarket function with on-chain data fetching
    - Implement getGroupMarkets function with filtering
    - Implement syncMarketData function
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 6.1, 6.5, 10.1, 10.2_

  - [ ]* 7.2 Write property test for status transitions
    - **Property 3: Status Transition Validity**
    - **Validates: Requirements 2.2, 3.3**

  - [ ]* 7.3 Write property test for market creation authorization
    - **Property 8: Market Creation Authorization**
    - **Validates: Requirements 12.3**

  - [ ]* 7.4 Write unit tests for market service
    - Test market creation with valid data
    - Test market creation with invalid data
    - Test market queries and filtering
    - Test on-chain data fetching and caching
    - _Requirements: 2.1, 2.3, 10.5_

- [x] 8. Implement Initialization Coordinator
  - [x] 8.1 Create initialization coordinator module
    - Implement initializeMarket with PostgreSQL advisory locks
    - Implement isInitializing check
    - Handle race conditions with database locking
    - _Requirements: 3.1, 3.2, 3.3, 3.6, 9.3_

  - [ ]* 8.2 Write property test for initialization idempotence
    - **Property 2: Market Initialization Idempotence**
    - **Validates: Requirements 3.6**

  - [ ]* 8.3 Write property test for concurrent initialization safety
    - **Property 9: Concurrent Initialization Safety**
    - **Validates: Requirements 9.3**

  - [ ]* 8.4 Write integration tests for initialization
    - Test single initialization
    - Test concurrent initialization (10 simultaneous requests)
    - Test initialization failure handling
    - Test lock timeout scenarios
    - _Requirements: 3.6, 9.3_

- [x] 9. Create Market API Endpoints
  - [x] 9.1 Implement market endpoints
    - POST /api/markets (create market)
    - POST /api/markets/:id/initialize (initialize on-chain)
    - GET /api/markets/:id (get market with stats)
    - GET /api/groups/:groupId/markets (list group markets)
    - POST /api/markets/:id/sync (sync on-chain data)
    - _Requirements: 2.1, 3.1, 5.1, 5.2, 5.3, 5.4, 5.5, 6.5, 10.1_

  - [ ]* 9.2 Write integration tests for market endpoints
    - Test market creation flow
    - Test initialization endpoint
    - Test market retrieval with on-chain data
    - Test group market listing with filters
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [ ] 10. Checkpoint - Ensure all backend tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [x] 11. Implement Admin Endpoints
  - [x] 11.1 Create admin authentication middleware
    - Verify admin role from JWT
    - Protect admin endpoints
    - _Requirements: 12.2_

  - [x] 11.2 Implement relay wallet admin endpoints
    - GET /api/admin/relay-wallet/balance
    - GET /api/admin/relay-wallet/transactions
    - _Requirements: 4.1, 4.5_

  - [ ]* 11.3 Write unit tests for admin endpoints
    - Test admin authentication
    - Test balance retrieval
    - Test transaction history
    - _Requirements: 12.2_

- [x] 12. Implement Error Handling
  - [x] 12.1 Create error handling utilities
    - Define error codes and messages
    - Implement error response formatter
    - Add retry logic for transient errors
    - _Requirements: 9.1, 9.2, 9.4_

  - [ ]* 12.2 Write unit tests for error handling
    - Test error response formatting
    - Test retry logic
    - Test different error scenarios
    - _Requirements: 9.1, 9.2, 9.5_

- [x] 13. Implement Rate Limiting
  - [x] 13.1 Add rate limiting middleware
    - Limit auth attempts per IP
    - Limit market creation per user
    - Limit initialization requests per market
    - _Requirements: 12.5_

  - [ ]* 13.2 Write unit tests for rate limiting
    - Test rate limit enforcement
    - Test rate limit reset
    - _Requirements: 12.5_

- [-] 14. Add Monitoring and Logging
  - [x] 14.1 Implement monitoring utilities
    - Log relay wallet transactions
    - Monitor relay wallet balance
    - Track initialization success/failure rates
    - Add performance metrics
    - _Requirements: 4.5_

  - [ ] 14.2 Set up alerts
    - Alert on low relay wallet balance
    - Alert on high failure rates
    - Alert on slow API responses
    - _Requirements: 4.4_

- [x] 15. Update User Controller
  - [x] 15.1 Remove or deprecate Privy authentication endpoint
    - Keep POST /api/auth/privy for backward compatibility (optional)
    - Update user lookup to prioritize walletAddress
    - _Requirements: 8.4_

  - [x] 15.2 Update user-related endpoints
    - Ensure all endpoints work with wallet-based auth
    - Update user profile to display wallet address
    - _Requirements: 1.6_

  - [ ]* 15.3 Write integration tests for user endpoints
    - Test user profile retrieval with wallet auth
    - Test user stats with wallet auth
    - _Requirements: 1.6_

- [x] 16. Implement Background Jobs
  - [x] 16.1 Create sync job for on-chain data
    - Periodically sync ACTIVE markets (every 1 minute)
    - Update cached stats in database
    - _Requirements: 6.5_

  - [x] 16.2 Create cleanup job for expired nonces
    - Remove expired nonces from Redis (every 5 minutes)
    - _Requirements: 1.1_

  - [x] 16.3 Create monitoring job for relay wallet
    - Check balance every 1 minute
    - Log warnings when below threshold
    - _Requirements: 4.2, 4.4_

  - [ ]* 16.4 Write unit tests for background jobs
    - Test sync job execution
    - Test cleanup job execution
    - Test monitoring job execution
    - _Requirements: 6.5_

- [ ] 17. Checkpoint - Full backend integration test
  - Ensure all tests pass, ask the user if questions arise.

- [x] 18. Update Environment Configuration
  - [x] 18.1 Add new environment variables
    - RELAY_WALLET_PRIVATE_KEY
    - RELAY_WALLET_MIN_BALANCE
    - MOVEMENT_RPC_URL
    - CONTRACT_ADDRESS
    - REDIS_URL (if not already present)
    - _Requirements: 4.1, 12.1_

  - [x] 18.2 Update .env.example file
    - Document all new environment variables
    - Provide example values
    - _Requirements: 4.1_

- [x] 19. Write API Documentation
  - [x] 19.1 Document authentication endpoints
    - POST /api/auth/wallet/message
    - POST /api/auth/wallet/verify
    - GET /api/auth/me
    - _Requirements: 1.1, 1.2, 1.3_

  - [x] 19.2 Document market endpoints
    - POST /api/markets
    - POST /api/markets/:id/initialize
    - GET /api/markets/:id
    - GET /api/groups/:groupId/markets
    - POST /api/markets/:id/sync
    - _Requirements: 2.1, 3.1, 5.1_

  - [x] 19.3 Document admin endpoints
    - GET /api/admin/relay-wallet/balance
    - GET /api/admin/relay-wallet/transactions
    - _Requirements: 4.1_

- [x] 20. Create Migration Guide
  - [x] 20.1 Write database migration instructions
    - Document migration steps
    - Provide rollback procedures
    - _Requirements: 8.1, 8.2, 8.3_

  - [x] 20.2 Write deployment checklist
    - Environment variable setup
    - Relay wallet funding
    - Database migration execution
    - Monitoring setup
    - _Requirements: 4.1, 4.2_

- [ ] 21. Final Integration Testing
  - [ ]* 21.1 Write end-to-end test for complete flow
    - User authentication with wallet
    - Market creation (off-chain)
    - Market initialization (on-chain)
    - Vote placement
    - Data synchronization
    - _Requirements: 7.1, 7.2, 7.3, 7.4_

  - [ ]* 21.2 Write property test for vote prerequisite enforcement
    - **Property 10: Vote Prerequisite Enforcement**
    - **Validates: Requirements 7.1, 7.2**

  - [ ]* 21.3 Write property test for on-chain ID uniqueness
    - **Property 4: On-Chain ID Uniqueness**
    - **Validates: Requirements 3.3**

- [ ] 22. Final Checkpoint - Production Readiness
  - Ensure all tests pass, ask the user if questions arise.
  - Verify all environment variables are documented
  - Verify monitoring and alerts are configured
  - Verify API documentation is complete

## Notes

- Tasks marked with `*` are optional and can be skipped for faster MVP
- Each task references specific requirements for traceability
- Checkpoints ensure incremental validation
- Property tests validate universal correctness properties with minimum 100 iterations
- Unit tests validate specific examples and edge cases
- Integration tests validate end-to-end flows
- The implementation follows a bottom-up approach: database → services → API → integration
- Relay wallet must be funded before deployment
- Redis must be available for nonce storage
- PostgreSQL advisory locks require PostgreSQL 9.1+
