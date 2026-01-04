# Full Degen Markets

## Overview

Full Degen Markets are traditional prediction markets with **full risk and full reward**. Winners take the entire losing pool.

## How It Works

### 1. Stake Your Tokens

Choose YES or NO and stake MOVE tokens.

```
Your bet: YES - 10 MOVE
Risk: You could lose all 10 MOVE
Reward: You could win from the NO pool
```

### 2. Wait for Deadline

Betting closes at the specified deadline.

### 3. Judge Resolves

After deadline, judge determines the outcome.

### 4. Winners Claim

If you predicted correctly, claim your share of the losing pool.

## Example Scenario

**Market:** "Will it rain in Jakarta tomorrow?"

**Betting:**

```
YES Pool:
├── Alice: 5 MOVE
├── Bob: 3 MOVE
└── Carol: 2 MOVE
Total YES: 10 MOVE

NO Pool:
├── Dave: 4 MOVE
└── Eve: 6 MOVE
Total NO: 10 MOVE
```

**Resolution:** Judge confirms it rained → YES wins

**Payouts:**

```
Alice: 5 + (5/10 × 10) = 5 + 5 = 10 MOVE (+100%)
Bob: 3 + (3/10 × 10) = 3 + 3 = 6 MOVE (+100%)
Carol: 2 + (2/10 × 10) = 2 + 2 = 4 MOVE (+100%)

Dave: Lost 4 MOVE
Eve: Lost 6 MOVE
```

## Risk & Reward

### High Risk

- You can lose your entire stake
- No principal protection
- Market could be declared INVALID

### High Reward

- Winners split the entire losing pool
- Potential for 2x-10x returns
- Proportional to your stake size

## Best For

✅ **High conviction predictions**  
✅ **Users comfortable with risk**  
✅ **Experienced prediction market users**  
✅ **Markets with clear outcomes**

❌ **Risk-averse users** → Try [Zero Loss Markets](Zero-Loss.md)  
❌ **Uncertain outcomes** → Wait for better markets  
❌ **New users** → Start with small stakes

## Tips for Success

### 1. Do Your Research

- Understand the market criteria
- Check judge's reputation
- Review similar past markets

### 2. Manage Your Risk

- Don't stake more than you can afford to lose
- Diversify across multiple markets
- Start small until you build confidence

### 3. Timing Matters

- Early bets get better odds
- Watch pool percentages
- Consider waiting if odds are unfavorable

### 4. Choose Good Markets

- Clear resolution criteria
- Trusted judges
- Reasonable deadlines
- Active participation

## Market Examples

### Good Markets ✅

```
"Will Bitcoin reach $100k by Dec 31, 2025?"
- Clear price target
- Specific deadline
- Verifiable outcome
```

```
"Will Team A win the championship?"
- Official results available
- Clear winner/loser
- Trusted sports judge
```

### Bad Markets ❌

```
"Will the weather be nice tomorrow?"
- Subjective criteria
- No clear definition of "nice"
- Hard to verify
```

```
"Will I be happy next year?"
- Completely subjective
- No way to verify
- Unfair to participants
```

## Fees

| Action        | Cost                    |
| ------------- | ----------------------- |
| Create Market | FREE (backend pays gas) |
| Place Vote    | Gas fee (~$0.01)        |
| Claim Reward  | Gas fee (~$0.01)        |
| Platform Fee  | 0% (for now)            |

---

**Next:** [Zero Loss Markets](Zero-Loss.md) | [How to Vote](../../How-It-Works/Voting-Guide.md)
