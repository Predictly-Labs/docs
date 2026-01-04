# Developers

Technical documentation for building with Predictly.

## Architecture

Predictly uses a hybrid on-chain/off-chain architecture:

- **Smart Contracts** - Move language on Movement Network
- **Backend API** - Express.js + TypeScript + PostgreSQL
- **Frontend** - Next.js 16 + React 19

[View Full Architecture →](Architecture.md)

## Smart Contracts

Move smart contracts handle:

- Market creation and escrow
- Voting and stake management
- Resolution and reward distribution
- Yield pools (coming soon)
- NFT roles (coming soon)

[Smart Contracts Documentation →](Smart-Contracts/README.md)

## Backend API

REST API for:

- User authentication (Privy)
- Group management
- Market data caching
- Event indexing
- IPFS uploads (Pinata)

[Backend API Documentation →](Backend-API/README.md)

## Database Schema

PostgreSQL database with Prisma ORM:

- Users, Groups, Members
- Markets, Votes
- Leaderboards, Badges

[Database Schema →](Database-Schema.md)

## Quick Start

### For Smart Contract Developers

```bash
cd contracts/predictly
aptos move compile
aptos move test
```

### For Backend Developers

```bash
cd backend
npm install
npm run dev
```

### For Frontend Developers

```bash
cd web
npm install
npm run dev
```

---

**Deployment Info:** [Contract Addresses](../Deployments/Contract-Addresses.md)
