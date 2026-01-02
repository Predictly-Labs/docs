# Implementation Plan

## Phase 1: Setup & Infrastructure

- [x] 1. Setup API Client and Types


  - [x] 1.1 Install dependencies (axios, @privy-io/react-auth)


    - Run `npm install axios @privy-io/react-auth`
    - _Requirements: 1.1_

  - [x] 1.2 Create environment configuration

    - Add `NEXT_PUBLIC_API_URL` to `.env.local`
    - Add `NEXT_PUBLIC_PRIVY_APP_ID` to `.env.local`
    - _Requirements: 1.4_
  - [x] 1.3 Create API client with interceptors


    - Create `lib/api.ts` with axios instance
    - Add request interceptor for auth token
    - Add response interceptor for 401 handling
    - _Requirements: 1.1, 1.2, 1.3_

  - [x] 1.4 Create TypeScript types for API

    - Create `types/api.ts` with User, Group, PredictionMarket, Vote types
    - Create `types/requests.ts` with input types
    - _Requirements: 1.1_

- [x] 2. Setup Auth Infrastructure



  - [x] 2.1 Setup Privy Provider

    - Wrap app with PrivyProvider in layout.tsx
    - Configure Privy with app ID
    - Added demo mode when Privy App ID not configured
    - _Requirements: 2.1_
  - [x] 2.2 Create AuthContext


    - Create `contexts/AuthContext.tsx`
    - Implement login, logout, user state
    - Store token in localStorage
    - Added loginDemo() for testing without Privy
    - _Requirements: 2.1, 2.2, 2.3, 2.4_

  - [x] 2.3 Create useAuth hook
    - Create `hooks/useAuth.ts`
    - Expose user, isAuthenticated, login, logout
    - _Requirements: 2.4_

## Phase 2: API Service Layer

- [x] 3. Create API Service Functions
  - [x] 3.1 Create auth API service
    - Create `lib/api/auth.ts`
    - Implement login, getMe, updateProfile
    - _Requirements: 2.1, 6.1, 6.2_
  - [x] 3.2 Create groups API service
    - Create `lib/api/groups.ts`
    - Implement list, getById, create, join, getMembers
    - _Requirements: 3.1, 3.2, 3.3, 3.4_
  - [x] 3.3 Create predictions API service
    - Create `lib/api/predictions.ts`
    - Implement list, getById, create, vote, resolve, claim, getMyVotes
    - _Requirements: 4.1, 4.2, 4.3, 5.1, 5.3_
  - [x] 3.4 Create upload API service
    - Create `lib/api/upload.ts`
    - Implement image upload to IPFS
    - _Requirements: 3.2_

## Phase 3: Custom Hooks

- [x] 4. Create Data Fetching Hooks

  - [x] 4.1 Create useGroups hook
    - Create `hooks/useGroups.ts`
    - Implement fetchGroups, createGroup, joinGroup
    - Handle loading and error states
    - _Requirements: 3.1, 3.2, 3.3_
  - [x] 4.2 Create useGroup hook (single group)
    - Create `hooks/useGroup.ts`
    - Fetch group details with members and markets
    - _Requirements: 3.4_
  - [x] 4.3 Create usePredictions hook
    - Create `hooks/usePredictions.ts`
    - Implement fetchMarkets, createMarket
    - _Requirements: 4.1, 4.2_
  - [x] 4.4 Create usePrediction hook (single market)
    - Create `hooks/usePrediction.ts`
    - Fetch market details, handle voting
    - _Requirements: 4.3, 4.4_
  - [x] 4.5 Create useMyVotes hook
    - Create `hooks/useMyVotes.ts`
    - Fetch user's vote history with yields
    - _Requirements: 5.1, 5.4_
  - [x] 4.6 Create useUser hook
    - Create `hooks/useUser.ts`
    - Fetch and update user profile
    - _Requirements: 6.1, 6.2, 6.3_

## Phase 4: Page Integration

- [x] 5. Integrate Dashboard Page
  - [x] 5.1 Create dashboard layout
    - Setup authenticated layout with sidebar
    - Add navigation between sections
    - _Requirements: 2.4_
  - [x] 5.2 Implement dashboard overview
    - Display user stats (predictions, accuracy, earnings)
    - Show recent activity from API
    - GroupCard fetches real groups
    - ActivityCard shows weekly vote activity
    - _Requirements: 6.3_

- [x] 6. Integrate Groups Pages
  - [x] 6.1 Implement groups list page
    - Fetch and display public groups from API
    - No mockup fallback - pure API consumption
    - _Requirements: 3.1_
  - [x] 6.2 Implement create group modal/page
    - CreateGroupModal with name, description, visibility
    - Integrated with useGroups hook
    - _Requirements: 3.2_
  - [x] 6.3 Implement join group flow
    - JoinGroupModal with invite code input
    - Success/error feedback
    - _Requirements: 3.3_
  - [x] 6.4 Implement group detail page
    - Display group info, members, markets
    - Fetches markets from API
    - _Requirements: 3.4_

- [x] 7. Integrate Predictions Pages
  - [x] 7.1 Implement predictions list in group
    - Display markets with status, percentages
    - Filter by status (active, resolved)
    - _Requirements: 4.1_
  - [x] 7.2 Implement create prediction modal
    - CreatePredictionModal with title, description, end date, market type, stakes
    - Integrated with usePredictions hook
    - _Requirements: 4.2_
  - [x] 7.3 Implement prediction detail page
    - PredictionDetailModal with market info, current odds
    - Shows participants and their votes
    - _Requirements: 4.4_
  - [x] 7.4 Implement voting UI
    - YES/NO buttons with stake input
    - Vote confirmation and success feedback
    - Updates percentages after vote
    - _Requirements: 4.3_

- [x] 8. Integrate Rewards Page
  - [x] 8.1 Implement vote history list
    - Display all user votes with market info
    - VoteHistoryCard component with full details
    - No mockup data - pure API consumption
    - _Requirements: 5.1_
  - [x] 8.2 Implement reward display
    - Show reward amount from API
    - Display real yield earnings from backend
    - _Requirements: 5.2, 5.4_
  - [x] 8.3 Implement claim reward button
    - Claim button on VoteHistoryCard for eligible rewards
    - Success feedback and state update
    - _Requirements: 5.3_

## Phase 5: Auth UI & Connect Wallet

- [x] 9. Add Login/Connect Wallet UI


  - [x] 9.1 Update Landing Page Navbar


    - Add "Connect Wallet" button next to "Launch App" button
    - Use useAuth hook to check authentication state
    - Show user avatar/address when connected
    - Add logout option in dropdown menu
    - File: `web/src/components/pages/(landing)/Navbar.tsx`
    - _Requirements: 2.1, 2.4_
  - [x] 9.2 Create ConnectWalletButton component


    - Create reusable `components/ui/ConnectWalletButton.tsx`
    - Handle Privy login flow on click
    - Show loading state during connection
    - Display truncated wallet address when connected
    - _Requirements: 2.1_

  - [x] 9.3 Create UserMenu component

    - Create `components/ui/UserMenu.tsx` for authenticated users
    - Show user avatar, display name, wallet address
    - Dropdown with Profile, Settings, Logout options
    - _Requirements: 2.3, 2.4_

  - [x] 9.4 Add auth state to Dashboard header

    - Update `web/src/components/pages/(app)/dashboard/index.tsx`
    - Add ConnectWalletButton/UserMenu to top right corner
    - Show "Connect Wallet to view your data" message when not authenticated
    - _Requirements: 2.4_
  - [x] 9.5 Add auth guard for app pages


    - Create `components/ui/AuthGuard.tsx` wrapper component
    - Redirect to landing or show connect prompt if not authenticated
    - Wrap dashboard and other app pages
    - _Requirements: 2.4_

## Phase 6: Polish & Testing

- [ ] 10. Add Loading & Error States
  - [ ] 10.1 Create loading skeletons
    - Skeleton components for lists and cards
    - _Requirements: 3.1, 4.1, 5.1_
  - [ ] 10.2 Create error boundaries
    - Error display components
    - Retry functionality
    - _Requirements: 1.3_
  - [ ] 10.3 Add toast notifications
    - Success/error toasts for actions
    - _Requirements: 3.2, 4.2, 4.3, 5.3_

- [ ] 11. Final Integration Testing
  - [ ] 11.1 Test auth flow end-to-end
    - Login with Privy -> Backend auth -> Token storage
    - _Requirements: 2.1, 2.2, 2.3_
  - [ ] 10.2 Test group operations
    - Create, join, view groups
    - _Requirements: 3.1, 3.2, 3.3, 3.4_
  - [ ] 10.3 Test prediction operations
    - Create market, vote, view results
    - _Requirements: 4.1, 4.2, 4.3, 4.4_
  - [ ] 10.4 Test rewards flow
    - View history, claim rewards
    - _Requirements: 5.1, 5.2, 5.3_
