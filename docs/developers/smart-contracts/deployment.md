# Smart Contract Deployment

This guide explains how to compile, test, and deploy the Predictly smart contracts to Movement Network.

## Prerequisites

Before deploying, ensure you have:

- **Aptos CLI** installed ([Installation Guide](https://aptos.dev/tools/aptos-cli/install-cli/))
- **Movement Network** wallet with MOVE tokens
- **Git** for cloning the repository

## Quick Start

```bash
# Clone repository
git clone https://github.com/your-username/predictly.git
cd predictly/contracts/predictly

# Compile
aptos move compile

# Test
aptos move test

# Deploy
aptos move publish --named-addresses predictly=default
```

## Detailed Steps

### 1. Install Aptos CLI

**macOS/Linux:**

```bash
curl -fsSL "https://aptos.dev/scripts/install_cli.py" | python3
```

**Windows:**

```powershell
iwr "https://aptos.dev/scripts/install_cli.py" -useb | Select-Object -ExpandProperty Content | python3
```

**Verify Installation:**

```bash
aptos --version
```

### 2. Initialize Wallet

If you don't have a wallet yet:

```bash
# Create new wallet
aptos init

# Follow prompts:
# - Choose network: testnet
# - Enter private key (or press Enter to generate new)
```

This creates `.aptos/config.yaml` with your wallet configuration.

### 3. Get Testnet Tokens

For Movement Testnet, you need MOVE tokens:

1. Visit [Movement Faucet](https://faucet.movementnetwork.xyz/)
2. Enter your wallet address
3. Request tokens

**Check Balance:**

```bash
aptos account list --account YOUR_ADDRESS
```

### 4. Clone Repository

```bash
git clone https://github.com/your-username/predictly.git
cd predictly/contracts/predictly
```

### 5. Configure Network

Edit `Move.toml` to set your address:

```toml
[addresses]
predictly = "YOUR_WALLET_ADDRESS"
```

Or use the `--named-addresses` flag when deploying.

### 6. Compile Contract

```bash
aptos move compile --package-dir .
```

**Expected Output:**

```
Compiling, may take a little while to download git dependencies...
INCLUDING DEPENDENCY AptosFramework
INCLUDING DEPENDENCY AptosStdlib
INCLUDING DEPENDENCY MoveStdlib
BUILDING predictly
Success
```

**Troubleshooting:**

- If compilation fails, check Move.toml dependencies
- Ensure you're using compatible Aptos CLI version
- Check for syntax errors in .move files

### 7. Run Tests

```bash
aptos move test --package-dir .
```

**Expected Output:**

```
Running Move unit tests
[ PASS    ] 0x916...::market::test_create_market
[ PASS    ] 0x916...::market::test_place_vote
[ PASS    ] 0x916...::market::test_resolve
[ PASS    ] 0x916...::market::test_claim_reward
Test result: OK. Total tests: 4; passed: 4; failed: 0
```

**Run Specific Test:**

```bash
aptos move test --package-dir . --filter test_create_market
```

### 8. Deploy to Testnet

```bash
aptos move publish \
  --package-dir . \
  --named-addresses predictly=default \
  --assume-yes
```

**What Happens:**

1. Contract is compiled
2. Transaction is built
3. You're prompted to confirm
4. Contract is deployed to blockchain
5. Contract address is returned

**Expected Output:**

```
Compiling, may take a little while...
Success
Do you want to submit a transaction for a range of [X - Y] Octas at a gas unit price of Z Octas? [yes/no]
> yes

Transaction submitted: 0xabc...
{
  "Result": {
    "transaction_hash": "0xabc...",
    "gas_used": 1234,
    "gas_unit_price": 100,
    "sender": "0x916...",
    "success": true
  }
}
```

### 9. Verify Deployment

**Via Explorer:**

1. Go to [Movement Explorer](https://explorer.movementnetwork.xyz/?network=bardock+testnet)
2. Search for your address
3. Click "Modules" tab
4. You should see `predictly::market`

**Via CLI:**

```bash
aptos move view \
  --function-id YOUR_ADDRESS::market::get_market_count \
  --args address:YOUR_ADDRESS
```

If it returns `["0"]`, deployment was successful!

## Deploy to Mainnet

> **⚠️ Warning:** Deploying to mainnet costs real MOVE tokens and is permanent!

### Prerequisites

- Sufficient MOVE tokens for gas
- Thoroughly tested contract
- Audited code (recommended)

### Steps

1. **Update Network Configuration**

```bash
aptos init --network mainnet
```

2. **Update Move.toml**

Ensure all dependencies point to mainnet versions.

3. **Final Testing**

```bash
aptos move test --package-dir .
```

4. **Deploy**

```bash
aptos move publish \
  --package-dir . \
  --named-addresses predictly=default \
  --network mainnet
```

5. **Verify**

Check on mainnet explorer and test with small amounts first.

## Upgrading Contracts

Move contracts are **immutable** by default. To upgrade:

### Option 1: Deploy New Version

Deploy a new contract with a different address and migrate users.

### Option 2: Use Upgrade Module

If you included upgrade capability in your contract:

```bash
aptos move upgrade \
  --package-dir . \
  --named-addresses predictly=YOUR_ADDRESS
```

**Note:** Predictly contracts are currently immutable for security.

## Configuration

### Move.toml

```toml
[package]
name = "predictly"
version = "1.0.0"
authors = ["Predictly Team"]

[addresses]
predictly = "_"

[dependencies.AptosFramework]
git = "https://github.com/aptos-labs/aptos-core.git"
rev = "mainnet"
subdir = "aptos-move/framework/aptos-framework"

[dependencies.AptosStdlib]
git = "https://github.com/aptos-labs/aptos-core.git"
rev = "mainnet"
subdir = "aptos-move/framework/aptos-stdlib"

[dependencies.MoveStdlib]
git = "https://github.com/aptos-labs/aptos-core.git"
rev = "mainnet"
subdir = "aptos-move/framework/move-stdlib"
```

### Network Configuration

**Testnet:**

```yaml
# .aptos/config.yaml
profiles:
  default:
    network: Testnet
    rest_url: "https://testnet.movementnetwork.xyz/v1"
    faucet_url: "https://faucet.movementnetwork.xyz"
```

**Mainnet:**

```yaml
profiles:
  default:
    network: Mainnet
    rest_url: "https://mainnet.movementnetwork.xyz/v1"
```

## Gas Costs

Typical deployment costs:

| Action          | Testnet      | Mainnet (est.) |
| --------------- | ------------ | -------------- |
| Deploy Contract | ~0.01 MOVE   | ~0.01 MOVE     |
| Create Market   | ~0.001 MOVE  | ~0.001 MOVE    |
| Place Vote      | ~0.0005 MOVE | ~0.0005 MOVE   |

**Note:** Actual costs vary based on contract size and network congestion.

## Troubleshooting

### Common Issues

**1. "Module already exists"**

- Contract already deployed to this address
- Use a different address or upgrade mechanism

**2. "Insufficient gas"**

- Get more MOVE tokens from faucet
- Check wallet balance

**3. "Compilation failed"**

- Check Move syntax
- Verify dependencies in Move.toml
- Update Aptos CLI to latest version

**4. "Network timeout"**

- Check internet connection
- Try different RPC endpoint
- Wait and retry

### Getting Help

- **[Aptos Discord](https://discord.gg/aptoslabs)** - Aptos community
- **[Movement Discord](https://discord.gg/movementnetwork)** - Movement Network support
- **[GitHub Issues](https://github.com/your-username/predictly/issues)** - Report bugs

## Best Practices

### Before Deployment

✅ **Test Thoroughly** - Run all unit tests  
✅ **Code Review** - Have others review your code  
✅ **Audit** - Consider professional audit for mainnet  
✅ **Document** - Update documentation  
✅ **Backup** - Save private keys securely

### After Deployment

✅ **Verify** - Test deployed contract  
✅ **Monitor** - Watch for errors  
✅ **Document Address** - Save contract address  
✅ **Update Frontend** - Point to new contract  
✅ **Announce** - Inform users

## Security Checklist

- [ ] All tests passing
- [ ] No hardcoded private keys
- [ ] Access controls implemented
- [ ] Input validation added
- [ ] Overflow protection
- [ ] Reentrancy guards (if applicable)
- [ ] Emergency pause mechanism (if needed)
- [ ] Code audited (for mainnet)

## Next Steps

After deployment:

1. **[Update Backend](../backend-api/deployment.md)** - Configure backend with new contract address
2. **[Update Frontend](../frontend/setup.md)** - Point frontend to new contract
3. **[Test Integration](../testing/integration-testing.md)** - Test end-to-end flow
4. **[Monitor](../../deployments/contract-addresses.md)** - Add to deployment docs

---

**Related Documentation:**

- [Contract Overview](overview.md)
- [View Functions](view-functions.md)
- [Entry Functions](entry-functions.md)
- [Testing Guide](../testing/smart-contracts.md)
