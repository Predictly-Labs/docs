# Zero Loss Markets

## Overview

Zero Loss Markets use **DeFi yield farming** to protect your principal. You never lose your stake - only the yield is distributed as rewards.

> **Status:** Coming Soon (Post-Hackathon)

## How It Works

### 1. Stake Your Tokens

Your MOVE tokens are deposited into a yield-generating strategy.

```
Your stake: 10 MOVE
Risk: ZERO - You always get your 10 MOVE back
Reward: Share of the yield generated
```

### 2. Yield Generation

While the market is active, your stake earns yield.

```
10 MOVE staked for 30 days
Yield rate: 5% APY
Yield generated: ~0.04 MOVE
```

### 3. Resolution

Judge determines the outcome.

### 4. Distribution

- **Everyone gets their principal back**
- **Winners split the total yield**

## Example Scenario

**Market:** "Will Bitcoin reach $100k by end of 2025?"  
**Duration:** 365 days  
**Yield Rate:** 5% APY

**Betting:**

```
YES Pool:
├── Alice: 100 MOVE
├── Bob: 50 MOVE
└── Carol: 50 MOVE
Total YES: 200 MOVE → Yield: ~10 MOVE

NO Pool:
├── Dave: 100 MOVE
└── Eve: 100 MOVE
Total NO: 200 MOVE → Yield: ~10 MOVE

Total Yield Pool: ~20 MOVE
```

**Resolution:** YES wins

**Payouts:**

```
Winners (YES):
Alice: 100 + (100/200 × 20) = 100 + 10 = 110 MOVE
Bob: 50 + (50/200 × 20) = 50 + 5 = 55 MOVE
Carol: 50 + (50/200 × 20) = 50 + 5 = 55 MOVE

Losers (NO):
Dave: 100 MOVE (principal returned)
Eve: 100 MOVE (principal returned)
```

## Risk & Reward

### Zero Risk ✅

- Principal is always protected
- You can't lose your stake
- Perfect for risk-averse users

### Lower Reward

- Only earn the yield (typically 5-10% APY)
- Smaller returns compared to Full Degen
- Longer time = more yield

## Yield Strategies

### Movement Network DeFi

- Staking protocols
- Liquidity pools
- Lending platforms

### Safety First

- Audited protocols only
- Conservative strategies
- Insurance coverage (when available)

## Best For

✅ **Risk-averse users**  
✅ **Long-term predictions**  
✅ **Learning prediction markets**  
✅ **Large stakes**  
✅ **Uncertain outcomes**

❌ **Quick profits** → Try [Full Degen](Full-Degen.md)  
❌ **Short-term markets** → Not enough yield  
❌ **High-risk appetite** → Full Degen has better returns

## Comparison

| Aspect         | Full Degen      | Zero Loss                  |
| -------------- | --------------- | -------------------------- |
| **Risk**       | High (lose all) | None (principal protected) |
| **Reward**     | High (2x-10x)   | Low (yield only, ~5-10%)   |
| **Duration**   | Any             | Better for 30+ days        |
| **Best For**   | High conviction | Risk-averse                |
| **Complexity** | Simple          | Moderate                   |

## Technical Details

### Yield Sources

```
User Stake (10 MOVE)
    ↓
Yield Protocol (e.g., Movement Staking)
    ↓
Generate Yield (~5% APY)
    ↓
Yield Pool (distributed to winners)
```

### Smart Contract Flow

```solidity
1. User stakes → Tokens sent to yield protocol
2. Yield accrues → Tracked in contract
3. Market resolves → Determine winners
4. Distribution:
   - Return principal to everyone
   - Split yield among winners
```

## Fees

| Action             | Cost             |
| ------------------ | ---------------- |
| Create Market      | FREE             |
| Place Vote         | Gas fee (~$0.01) |
| Claim Reward       | Gas fee (~$0.01) |
| Yield Protocol Fee | ~0.5% of yield   |

## Roadmap

### Phase 1 (Q1 2025)

- [ ] Integrate with Movement staking
- [ ] Test on testnet
- [ ] Security audit

### Phase 2 (Q2 2025)

- [ ] Launch on mainnet
- [ ] Add multiple yield sources
- [ ] Insurance coverage

### Phase 3 (Q3 2025)

- [ ] Advanced strategies
- [ ] Auto-compounding
- [ ] Yield optimization

---

**Status:** Coming Soon  
**Expected Launch:** Q1 2025  
**Current:** Available on testnet for testing

---

**Next:** [Private Markets](Private.md) | [Full Degen Markets](Full-Degen.md)
