# Contract Addresses

## Movement Testnet

| Property | Value                                                                                                                                                                       |
| -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Network  | Movement Testnet (Bardock)                                                                                                                                                  |
| Contract | `0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565`                                                                                                        |
| Module   | `predictly::market`                                                                                                                                                         |
| RPC      | `https://testnet.movementnetwork.xyz/v1`                                                                                                                                    |
| Explorer | [View Contract](https://explorer.movementnetwork.xyz/account/0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565/modules/run/market?network=bardock+testnet) |
| Faucet   | [Get Tokens](https://faucet.movementnetwork.xyz/)                                                                                                                           |

## Movement Mainnet

| Property | Value       |
| -------- | ----------- |
| Status   | Coming Soon |

## Backend API

| Environment | URL                                     |
| ----------- | --------------------------------------- |
| Production  | `https://backend-3ufs.onrender.com`     |
| API Docs    | `https://backend-3ufs.onrender.com/api` |

## Integration

### Frontend

```env
NEXT_PUBLIC_CONTRACT_ADDRESS=0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
NEXT_PUBLIC_RPC_URL=https://testnet.movementnetwork.xyz/v1
NEXT_PUBLIC_API_URL=https://backend-3ufs.onrender.com
```

### Backend

```env
MOVEMENT_RPC_URL=https://testnet.movementnetwork.xyz/v1
MOVEMENT_CONTRACT_ADDRESS=0x9161980be9b78e96ddae98ceb289f6f4cda5e4af70667667ff9af8438a94e565
```
