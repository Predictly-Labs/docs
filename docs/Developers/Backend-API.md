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

## Hybrid System

| Action        | Location  | Cost         |
| ------------- | --------- | ------------ |
| Create market | Off-chain | Free         |
| Initialize    | On-chain  | Backend pays |
| Vote          | On-chain  | User pays    |
