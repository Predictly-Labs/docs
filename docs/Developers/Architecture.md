# Architecture

## System Overview

Predictly uses a **hybrid on-chain/off-chain architecture** to balance decentralization with user experience.

```
┌─────────────────────────────────────────────────────────────────┐
│                            USERS                                 │
│                   (Browser / Mobile / Wallet)                    │
└────────────────────────────┬────────────────────────────────────┘
                             │
                             ▼
              ┌──────────────────────────────┐
              │    WALLET CONNECTION         │
              │  (Nightly/Petra/Martian)     │
              │                              │
              │  - Sign Transactions         │
              │  - View Balances             │
              │  - Manage Assets             │
              └──────────────┬───────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│                         FRONTEND                                 │
│                   (Next.js 16 + React 19)                        │
│                                                                  │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐        │
│  │ Landing  │  │Dashboard │  │  Groups  │  │ Markets  │        │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘        │
│                                                                  │
│  ┌────────────────────────────────────────────────────┐         │
│  │         Movement SDK (Client-side)                 │         │
│  │       Direct blockchain interactions               │         │
│  └────────────────────────────────────────────────────┘         │
└───────────────┬─────────────────────────┬───────────────────────┘
                │                         │
    REST API    │                         │  Direct Blockchain TX
                │                         │
                ▼                         ▼
┌────────────────────────┐    ┌────────────────────────┐
│   BACKEND              │    │  MOVEMENT NETWORK      │
│   (Express.js)         │    │                        │
│                        │    │  ┌──────────────────┐  │
│  ┌──────────────────┐  │    │  │ Smart Contracts  │  │
│  │   API Routes     │  │    │  │  (Move Language) │  │
│  │                  │  │    │  │                  │  │
│  │  /api/groups     │  │    │  │  - Market Logic  │  │
│  │  /api/users      │  │    │  │  - Escrow        │  │
│  │  /api/markets    │  │    │  │  - Rewards       │  │
│  │  /api/upload     │  │    │  │  - Resolution    │  │
│  └──────────────────┘  │    │  └──────────────────┘  │
│                        │    │                        │
│  ┌──────────────────┐  │    │                        │
│  │    Services      │◄─┼────┼──  Events              │
│  │                  │  │    │                        │
│  │  - GroupService  │  │    └────────────────────────┘
│  │  - UserService   │  │
│  │  - MarketService │  │
│  │  - PinataService │  │
│  │  - EventIndexer  │  │
│  └──────────────────┘  │
│                        │
└───────┬────────────────┘
        │
        ▼
┌────────────────┐    ┌────────────────┐
│   PostgreSQL   │    │  Pinata (IPFS) │
│                │    │                │
│  - Users       │    │  - Images      │
│  - Groups      │    │  - Avatars     │
│  - Markets     │    │  - Icons       │
│  - Votes       │    │  - Metadata    │
│  - Leaderboard │    │                │
└────────────────┘    └────────────────┘
```

## Technology Stack

| Layer          | Technology              | Purpose                    |
| -------------- | ----------------------- | -------------------------- |
| **Frontend**   | Next.js 16 + React 19   | UI/UX, wallet connection   |
| **Backend**    | Express.js + TypeScript | API, caching, indexer      |
| **Database**   | PostgreSQL + Prisma     | Off-chain data storage     |
| **Storage**    | Pinata IPFS             | Decentralized file storage |
| **Blockchain** | Movement Network (Move) | Markets, stakes, rewards   |
| **Wallets**    | Nightly, Petra, Martian | Web3 wallet integration    |

## Data Ownership

### On-Chain (Movement Network)

**Source of truth for:**

- Market creation & parameters
- Stakes & deposits
- Bet positions
- Reward distribution
- Token balances
- Yield pools
- NFT ownership

**Characteristics:**

- 🔒 Immutable
- 🌐 Transparent
- ⛓️ Trustless

### Off-Chain (PostgreSQL)

**Source of truth for:**

- User profiles
- Group management
- Social features
- Leaderboards
- Notification preferences
- Cached market stats
- Invite codes
- Activity history

**Characteristics:**

- ⚡ Fast queries
- 🔄 Real-time updates
- 📈 Analytics

## Hybrid Flow Example

### Creating a Market

```
1. User fills form (Frontend)
   ↓
2. POST /api/markets (Backend)
   - Validate data
   - Save to PostgreSQL
   - Upload image to IPFS
   - Return market ID
   ↓
3. Initialize on-chain (Frontend)
   - Call smart contract
   - User signs transaction
   - Market deployed to blockchain
   ↓
4. Sync back (Backend)
   - Listen for MarketCreated event
   - Update PostgreSQL with on-chain ID
   - Market now ACTIVE
```

### Voting on a Market

```
1. User selects YES/NO + amount (Frontend)
   ↓
2. Direct blockchain call (Frontend)
   - place_vote(market_id, prediction, amount)
   - User signs transaction
   - Tokens locked in contract
   ↓
3. Event indexer (Backend)
   - Detect VotePlaced event
   - Update PostgreSQL cache
   - Update leaderboard
   - Send notification
```

## Event-Based Sync

Backend listens for blockchain events and syncs to database:

```typescript
// Event Listener Service
setInterval(async () => {
  const events = await movement.getEvents(lastBlock);

  for (const event of events) {
    switch (event.type) {
      case "MarketCreated":
        await syncMarketCreated(event);
        break;
      case "VotePlaced":
        await syncVotePlaced(event);
        break;
      case "MarketResolved":
        await syncMarketResolved(event);
        break;
    }
  }
}, 5000); // Poll every 5 seconds
```

## Security Architecture

### Smart Contract Security

- **Move Language** - Resource-oriented, prevents common vulnerabilities
- **Formal Verification** - Mathematical proof of correctness
- **Access Control** - Role-based permissions
- **Reentrancy Guards** - Protection against attacks

### Backend Security

- **JWT Authentication** - Secure API access
- **Rate Limiting** - Prevent abuse
- **Input Validation** - Sanitize all inputs
- **CORS** - Restrict cross-origin requests

### Frontend Security

- **Wallet Signing** - All transactions signed by user
- **No Private Keys** - Never store or transmit keys
- **HTTPS Only** - Encrypted communication
- **CSP Headers** - Content Security Policy

---

**Next:** [Smart Contracts](Smart-Contracts/README.md) | [Backend API](Backend-API/README.md)
