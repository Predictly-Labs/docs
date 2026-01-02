# Contract Addresses & Deployments

This page lists all deployed Predictly smart contracts and related infrastructure across different networks.

## Movement Network

### Testnet (Bardock)

| Component          | Address/URL                                                                                                                                                                 |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Smart Contract** | `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565`                                                                                                        |
| **Module**         | `predictly::market`                                                                                                                                                         |
| **Network**        | Movement Testnet (Bardock)                                                                                                                                                  |
| **RPC URL**        | `https://testnet.movementnetwork.xyz/v1`                                                                                                                                    |
| **Explorer**       | [View Contract](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet) |
| **Faucet**         | [Get Test Tokens](https://faucet.movementnetwork.xyz/)                                                                                                                      |

### Mainnet

| Component          | Status           |
| ------------------ | ---------------- |
| **Smart Contract** | Coming Soon      |
| **Network**        | Movement Mainnet |
| **RPC URL**        | TBA              |

---

## Backend API

### Production

| Component             | URL                                            |
| --------------------- | ---------------------------------------------- |
| **API Base URL**      | `https://backend-3ufs.onrender.com`            |
| **API Documentation** | `https://backend-3ufs.onrender.com/api`        |
| **Health Check**      | `https://backend-3ufs.onrender.com/api/health` |
| **Environment**       | Production                                     |
| **Network**           | Movement Testnet                               |

### Development

| Component             | URL                         |
| --------------------- | --------------------------- |
| **API Base URL**      | `http://localhost:3001`     |
| **API Documentation** | `http://localhost:3001/api` |
| **Environment**       | Development                 |

---

## Frontend Application

### Production

| Component       | URL         |
| --------------- | ----------- |
| **Application** | Coming Soon |
| **Environment** | Production  |

### Development

| Component       | URL                     |
| --------------- | ----------------------- |
| **Application** | `http://localhost:3000` |
| **Environment** | Development             |

---

## Contract Details

### Smart Contract Functions

**View Functions** (Read-only, no gas):

- `get_market_count`
- `get_market_status`
- `get_market_outcome`
- `get_market_pools`
- `get_percentages`
- `get_participant_count`
- `get_vote_prediction`
- `get_vote_amount`
- `calculate_reward`

**Entry Functions** (Requires gas):

- `create_market`
- `place_vote`
- `resolve`
- `claim_reward`

[View full contract documentation →](../developers/smart-contracts/overview.md)

---

## Network Information

### Movement Testnet (Bardock)

| Property       | Value     |
| -------------- | --------- |
| **Chain ID**   | TBA       |
| **Currency**   | MOVE      |
| **Decimals**   | 8         |
| **Block Time** | ~1 second |
| **Finality**   | Instant   |

### RPC Endpoints

**Primary:**

```
https://testnet.movementnetwork.xyz/v1
```

**Backup:**

```
TBA
```

### Explorer Links

**Account Explorer:**

```
https://explorer.movementnetwork.xyz/account/{ADDRESS}?network=bardock+testnet
```

**Transaction Explorer:**

```
https://explorer.movementnetwork.xyz/txn/{HASH}?network=bardock+testnet
```

---

## Integration Endpoints

### For Developers

**Smart Contract Integration:**

```typescript
const CONTRACT_ADDRESS =
  "0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565";
const RPC_URL = "https://testnet.movementnetwork.xyz/v1";
```

**Backend API Integration:**

```typescript
const API_BASE_URL = "https://backend-3ufs.onrender.com";
const API_DOCS_URL = "https://backend-3ufs.onrender.com/api";
```

[Integration guides →](../developers/frontend/api-integration.md)

---

## Deployment History

### Smart Contract

| Version | Date       | Network | Address     | Changes            |
| ------- | ---------- | ------- | ----------- | ------------------ |
| v1.0.0  | 2024-12-XX | Testnet | `0x9161...` | Initial deployment |

### Backend API

| Version | Date       | Environment | URL                         | Changes            |
| ------- | ---------- | ----------- | --------------------------- | ------------------ |
| v1.0.0  | 2024-12-XX | Production  | `backend-3ufs.onrender.com` | Initial deployment |

---

## Environment Variables

### Frontend

```env
# Network
NEXT_PUBLIC_NETWORK=testnet
NEXT_PUBLIC_RPC_URL=https://testnet.movementnetwork.xyz/v1

# Contract
NEXT_PUBLIC_CONTRACT_ADDRESS=0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565

# Backend
NEXT_PUBLIC_API_URL=https://backend-3ufs.onrender.com
```

### Backend

```env
# Movement Network
MOVEMENT_RPC_URL=https://testnet.movementnetwork.xyz/v1
MOVEMENT_CONTRACT_ADDRESS=0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
RELAY_WALLET_PRIVATE_KEY=your_private_key
```

---

## Status & Monitoring

### Contract Status

Check contract health:

```bash
aptos move view \
  --function-id 0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565::market::get_market_count \
  --args address:0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
```

### API Status

Check API health:

```bash
curl https://backend-3ufs.onrender.com/api/health
```

Expected response:

```json
{
  "status": "ok",
  "timestamp": "2024-12-XX..."
}
```

---

## Verification

### Verify Contract

1. Visit [Movement Explorer](https://explorer.movementnetwork.xyz/?network=bardock+testnet)
2. Search for: `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565`
3. Click **"Modules"** tab
4. Verify `predictly::market` module exists

### Verify Backend

1. Visit: `https://backend-3ufs.onrender.com/api`
2. Should see Swagger API documentation
3. Try health endpoint
4. Check contract info endpoint

---

## Migration Guide

### Updating Contract Address

If contract address changes:

**Frontend:**

1. Update `.env.local`
2. Update `NEXT_PUBLIC_CONTRACT_ADDRESS`
3. Rebuild application

**Backend:**

1. Update `.env`
2. Update `MOVEMENT_CONTRACT_ADDRESS`
3. Restart server

**Documentation:**

1. Update this page
2. Update integration guides
3. Notify users

---

## Support

### Issues with Deployments?

- **Smart Contract**: [GitHub Issues](https://github.com/your-username/predictly/issues)
- **Backend API**: [GitHub Issues](https://github.com/your-username/predictly/issues)
- **Frontend**: [GitHub Issues](https://github.com/your-username/predictly/issues)

### Network Issues?

- **Movement Network**: [Movement Discord](https://discord.gg/movementnetwork)
- **RPC Issues**: Check [Status Page](https://status.movementnetwork.xyz/)

---

## Related Documentation

- **[Smart Contracts](../developers/smart-contracts/overview.md)** - Contract documentation
- **[Backend API](../developers/backend-api/overview.md)** - API documentation
- **[Frontend Setup](../developers/frontend/setup.md)** - Frontend integration
- **[Deployment Guide](../developers/smart-contracts/deployment.md)** - Deploy your own

---

**Last Updated:** 2024-12-XX  
**Network:** Movement Testnet (Bardock)  
**Status:** ✅ Operational
