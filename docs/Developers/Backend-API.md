# Backend API

## Overview

| Technology | Purpose       |
| ---------- | ------------- |
| Node.js    | Runtime       |
| Express.js | Web framework |
| TypeScript | Language      |
| PostgreSQL | Database      |
| Prisma     | ORM           |

## Base URLs

| Environment | URL                                 |
| ----------- | ----------------------------------- |
| Production  | `https://backend-3ufs.onrender.com` |
| Development | `http://localhost:3001`             |
| API Docs    | `/api` (Swagger UI)                 |

## Authentication

Wallet-based authentication:

1. Get message: `POST /api/auth/wallet/message`
2. Sign with wallet
3. Verify: `POST /api/auth/wallet/verify`
4. Use token: `Authorization: Bearer <token>`

### Example: Get Sign-In Message

**Request:**

```bash
curl -X POST https://backend-3ufs.onrender.com/api/auth/wallet/message \
  -H "Content-Type: application/json" \
  -d '{"walletAddress": "0xYourWalletAddress"}'
```

**Response:**

```json
{
  "message": "Sign this message to authenticate: nonce-12345"
}
```

### Example: Verify Signature

**Request:**

```bash
curl -X POST https://backend-3ufs.onrender.com/api/auth/wallet/verify \
  -H "Content-Type: application/json" \
  -d '{
    "walletAddress": "0xYourWalletAddress",
    "signature": "0xSignedMessage"
  }'
```

**Response:**

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": "user-id",
    "walletAddress": "0xYourWalletAddress"
  }
}
```

## Endpoints

### Authentication

| Method | Endpoint                   | Description          |
| ------ | -------------------------- | -------------------- |
| POST   | `/api/auth/wallet/message` | Get sign-in message  |
| POST   | `/api/auth/wallet/verify`  | Verify and get token |

### Users

| Method | Endpoint               | Description     |
| ------ | ---------------------- | --------------- |
| GET    | `/api/users/me`        | Current user    |
| GET    | `/api/users/:id/stats` | User statistics |

### Groups

| Method | Endpoint           | Description    |
| ------ | ------------------ | -------------- |
| POST   | `/api/groups`      | Create group   |
| GET    | `/api/groups`      | List groups    |
| POST   | `/api/groups/join` | Join with code |

### Markets

| Method | Endpoint                      | Description     |
| ------ | ----------------------------- | --------------- |
| POST   | `/api/markets`                | Create market   |
| GET    | `/api/markets/:id`            | Get market      |
| POST   | `/api/markets/:id/initialize` | Deploy to chain |

### Example: Create Market

**Request:**

```bash
curl -X POST https://backend-3ufs.onrender.com/api/markets \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Will Bitcoin reach $100k?",
    "description": "Bitcoin must reach or exceed $100,000 USD by Dec 31, 2025",
    "endTime": "2025-12-31T23:59:59Z",
    "groupId": "group-id-optional"
  }'
```

**Response:**

```json
{
  "id": "market-123",
  "title": "Will Bitcoin reach $100k?",
  "status": "PENDING",
  "createdAt": "2026-01-04T..."
}
```

## Hybrid System

| Action        | Location  | Cost         |
| ------------- | --------- | ------------ |
| Create market | Off-chain | Free         |
| Initialize    | On-chain  | Backend pays |
| Vote          | On-chain  | User pays    |

## Error Codes

| Code | Message               | Description                  |
| ---- | --------------------- | ---------------------------- |
| 401  | Unauthorized          | Invalid or missing JWT token |
| 403  | Forbidden             | Insufficient permissions     |
| 404  | Not Found             | Resource doesn't exist       |
| 429  | Too Many Requests     | Rate limit exceeded          |
| 500  | Internal Server Error | Server error                 |

## Rate Limiting

| Endpoint       | Limit               |
| -------------- | ------------------- |
| Authentication | 10 requests/minute  |
| Markets        | 30 requests/minute  |
| General        | 100 requests/minute |
