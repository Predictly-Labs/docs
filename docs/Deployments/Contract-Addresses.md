# Contract Addresses

## Movement Testnet (Bardock)

| Property | Value                                                                                                                                                                       |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Network  | Movement Testnet (Bardock)                                                                                                                                                  |
| Contract | `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565`                                                                                                        |
| Module   | `predictly::market`                                                                                                                                                         |
| RPC      | `https://testnet.movementnetwork.xyz/v1`                                                                                                                                    |
| Explorer | [View Contract](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet) |
| Faucet   | [Get Tokens](https://faucet.movementnetwork.xyz/)                                                                                                                           |
| Status   | ✅ Live on Testnet                                                                                                                                                          |

## Frontend Deployment

| Environment | URL                                                                            |
| ----------- | ------------------------------------------------------------------------------ |
| Production  | [https://predictly-movement.vercel.app](https://predictly-movement.vercel.app) |
| Platform    | Vercel                                                                         |
| Status      | ✅ Live                                                                        |

## Backend API

| Environment | URL                                     |
| ----------- | --------------------------------------- |
| Production  | `https://backend-3ufs.onrender.com`     |
| API Docs    | `https://backend-3ufs.onrender.com/api` |
| Platform    | Render                                  |
| Status      | ✅ Live                                 |

## Integration

### Frontend Environment Variables

```env
NEXT_PUBLIC_CONTRACT_ADDRESS=0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
NEXT_PUBLIC_RPC_URL=https://testnet.movementnetwork.xyz/v1
NEXT_PUBLIC_API_URL=https://backend-3ufs.onrender.com
```

### Backend Environment Variables

```env
MOVEMENT_RPC_URL=https://testnet.movementnetwork.xyz/v1
MOVEMENT_CONTRACT_ADDRESS=0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
```

---

**For Developers:** [Architecture](../Developers/Architecture.md) | [Smart Contracts](../Developers/README.md)
