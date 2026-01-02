# Implementation Plan

## Phase 1: Setup & Dependencies

- [ ] 1. Install dependencies and configure environment
  - [ ] 1.1 Install @aptos-labs/ts-sdk package
    - Add to web/package.json
    - _Requirements: 7.1_
  - [ ] 1.2 Add environment variables
    - Add NEXT_PUBLIC_CONTRACT_ADDRESS to .env.local
    - Add NEXT_PUBLIC_MOVEMENT_RPC_URL to .env.local
    - Update .env.example with new variables
    - _Requirements: 7.4, 7.5_

## Phase 2: Contract Service

- [ ] 2. Create contract service
  - [ ] 2.1 Create conversion utilities
    - Create `web/src/lib/contract.ts`
    - Implement octasToMove function
    - Implement moveToOctas function
    - Implement basisPointsToPercent function
    - _Requirements: 2.2, 2.3, 4.2, 7.3_
  - [ ]* 2.2 Write property tests for conversion functions
    - **Property 1: Octas to MOVE conversion preserves value**
    - **Property 2: MOVE to octas conversion preserves value**
    - **Property 3: Basis points to percentage conversion**
    - **Validates: Requirements 2.2, 2.3, 4.2**
  - [ ] 2.3 Implement view functions
    - Implement getMarketCount
    - Implement getMarketState
    - Implement getMarketPools
    - Implement getPercentages
    - Implement getParticipantCount
    - Implement getVoteInfo
    - Implement calculateReward
    - _Requirements: 2.1, 7.1_
  - [ ] 2.4 Implement payload builders
    - Implement buildCreateMarketPayload
    - Implement buildPlaceVotePayload
    - Implement buildResolvePayload
    - Implement buildClaimRewardPayload
    - _Requirements: 3.1, 4.1, 5.1, 6.1, 7.2_
  - [ ]* 2.5 Write property tests for payload builders
    - **Property 4: Place vote payload structure**
    - **Property 5: Create market payload structure**
    - **Property 6: Resolve payload structure**
    - **Property 7: Claim reward payload structure**
    - **Validates: Requirements 3.1, 4.1, 5.1, 6.1**

- [ ] 3. Checkpoint - Verify contract service
  - Ensure all tests pass, ask the user if questions arise

## Phase 3: React Integration

- [ ] 4. Create wallet hook
  - [ ] 4.1 Create useWallet hook
    - Create `web/src/hooks/useWallet.ts`
    - Integrate with Privy usePrivy hook
    - Return isConnected, address, connect, disconnect
    - _Requirements: 1.1, 1.2, 1.3, 1.5_
  - [ ] 4.2 Export from hooks index
    - Add useWallet to `web/src/hooks/index.ts`
    - _Requirements: 1.1_

- [ ] 5. Create contract provider and hook
  - [ ] 5.1 Create ContractProvider
    - Create `web/src/providers/ContractProvider.tsx`
    - Initialize Aptos client with Movement RPC
    - Provide view functions via context
    - Provide transaction functions via context
    - Track isPending and error state
    - _Requirements: 2.1, 3.1, 4.1, 5.1, 6.1_
  - [ ] 5.2 Create useContract hook
    - Create `web/src/hooks/useContract.ts`
    - Return context value from ContractProvider
    - _Requirements: 7.1_
  - [ ] 5.3 Add ContractProvider to app layout
    - Update `web/src/app/layout.tsx` to include ContractProvider
    - _Requirements: 7.1_
  - [ ] 5.4 Export from hooks index
    - Add useContract to `web/src/hooks/index.ts`
    - _Requirements: 7.1_

- [ ] 6. Checkpoint - Verify React integration
  - Ensure all tests pass, ask the user if questions arise

## Phase 4: Transaction Handling

- [ ] 7. Implement transaction signing with Privy
  - [ ] 7.1 Add transaction signing to ContractProvider
    - Use Privy wallet provider for signing
    - Handle transaction submission
    - Parse transaction result
    - _Requirements: 3.2, 3.3_
  - [ ] 7.2 Implement error handling
    - Parse contract error codes
    - Map to user-friendly messages
    - Display errors in UI
    - _Requirements: 3.5, 4.4, 5.3, 6.4_
  - [ ] 7.3 Implement loading states
    - Track pending transactions
    - Show loading indicator during signing
    - Show loading indicator during submission
    - _Requirements: 3.6_

- [ ] 8. Checkpoint - Verify transaction handling
  - Ensure all tests pass, ask the user if questions arise

## Phase 5: UI Integration

- [ ] 9. Update prediction hooks to use contract
  - [ ] 9.1 Update usePrediction hook
    - Add on-chain data fetching via useContract
    - Merge on-chain data with backend data
    - Handle fallback to backend data on error
    - _Requirements: 2.1, 2.4_
  - [ ] 9.2 Update vote function
    - Build transaction payload
    - Sign and submit via ContractProvider
    - Update UI on success
    - _Requirements: 3.1, 3.2, 3.3, 3.4_
  - [ ] 9.3 Update resolve function
    - Build transaction payload
    - Sign and submit via ContractProvider
    - Update UI on success
    - _Requirements: 5.1, 5.2_
  - [ ] 9.4 Update claim function
    - Build transaction payload
    - Sign and submit via ContractProvider
    - Display claimed amount
    - _Requirements: 6.1, 6.2, 6.3_

- [ ] 10. Add wallet connection UI
  - [ ] 10.1 Create WalletButton component
    - Show connect button when disconnected
    - Show address when connected
    - Handle connect/disconnect actions
    - _Requirements: 1.1, 1.3, 1.5_
  - [ ] 10.2 Add WalletButton to header/navbar
    - Display wallet status in navigation
    - _Requirements: 1.3_

- [ ] 11. Final Checkpoint
  - Test full flow: connect wallet → create market → vote → resolve → claim
  - Ensure all tests pass, ask the user if questions arise
