# Judge System

## Overview

The Judge System is Predictly's **decentralized oracle solution** using trusted friends instead of complex automated oracles.

## Concept: Trusted Middleman

Instead of relying on expensive oracles or centralized platforms, Predictly uses **people you trust** to verify outcomes.

### Why Judges?

**Traditional Oracles:**

- Expensive (high fees)
- Limited data sources
- Complex integration
- Not suitable for personal predictions

**Judge System:**

- FREE (your friends)
- Can verify anything
- Simple and flexible
- Perfect for social predictions

## How It Works

### 1. Group Creation

```
Admin creates group
    ↓
Admin assigns judges from members
    ↓
Judges can now resolve markets
```

### 2. Market Creation

```
Anyone creates market
    ↓
Market enters ACTIVE status
    ↓
Users place votes
```

### 3. Deadline Passes

```
Voting closes
    ↓
Market status: PENDING
    ↓
Waiting for judge resolution
```

### 4. Judge Resolution

```
Judge reviews real-world outcome
    ↓
Judge submits resolution (YES/NO/INVALID)
    ↓
Smart contract verifies judge authority
    ↓
Rewards distributed automatically
```

## Example Flow

### Market: "Will Bang Isal finish thesis by end of month?"

**Setup:**

```
Group: Kos Squad
Judge: Reza (trusted friend)
Deadline: Jan 31, 2026
```

**Betting Phase:**

```
YES Pool:
├── Dika: 5 MOVE
└── Budi: 2 MOVE
Total: 7 MOVE

NO Pool:
└── Ayu: 3 MOVE
Total: 3 MOVE
```

**Resolution Phase:**

```
Feb 1, 2026 - Deadline passed

Judge Reza:
1. Asks Bang Isal for proof
2. Reviews thesis submission receipt
3. Confirms: YES, thesis submitted ✅
4. Submits resolution on-chain
```

**Payout:**

```
Smart contract verifies:
- Reza is authorized judge ✅
- Market is in PENDING status ✅
- Deadline has passed ✅

Distributes rewards:
- Dika: 5 + (5/7 × 3) = 7.14 MOVE
- Budi: 2 + (2/7 × 3) = 2.86 MOVE
- Ayu: Lost 3 MOVE
```

## Judge Responsibilities

### Before Resolution

- Understand market criteria
- Know how to verify outcome
- Be available after deadline

### During Resolution

- Review real-world outcome objectively
- Gather evidence if needed
- Document verification process

### After Resolution

- Submit resolution promptly
- Respond to disputes if any
- Maintain reputation

## Verification Methods

### Physical Evidence

- Photos (weight loss, completed project)
- Screenshots (bank balance, test scores)
- Documents (receipts, certificates)

### Official Sources

- News articles
- Sports results
- Weather data
- Stock prices

### Personal Verification

- Witness testimony
- Group consensus
- Direct observation

## Trust Model

### Why It Works

**Social Accountability**

- Judges are your friends
- Reputation matters
- Bad judges get removed

**On-Chain Record**

- All resolutions recorded
- Transparent history
- Can't change past decisions

**Group Consensus**

- Can dispute if needed
- Admin can override
- Community governance

### Judge Reputation

Track judge performance:

```
Judge: Reza
├── Markets Resolved: 25
├── Disputes: 1 (4%)
├── Average Resolution Time: 2 hours
└── Trust Score: 96%
```

## Resolution Options

| Outcome     | When to Use                                              |
| ----------- | -------------------------------------------------------- |
| **YES**     | Prediction came true                                     |
| **NO**      | Prediction did not come true                             |
| **INVALID** | Market criteria unclear, unfair, or impossible to verify |

### INVALID Outcome

When market should be cancelled:

- Criteria was ambiguous
- Outcome impossible to verify
- Market was unfair
- External circumstances prevented resolution

**Effect:** Everyone gets refunded, no winners.

## Dispute Resolution

### If Resolution is Contested

1. **Member Raises Dispute**

   - Provide evidence
   - Explain disagreement

2. **Group Discussion**

   - Judge explains reasoning
   - Members review evidence

3. **Admin Review**

   - Admin makes final decision
   - Can override judge if needed

4. **Re-Resolution**
   - Market re-resolved if needed
   - Or declared INVALID with refunds

## Judge Selection

### Good Judge Qualities

✅ Trustworthy  
✅ Objective  
✅ Available  
✅ Knowledgeable about topic  
✅ Good communicator

### Avoid

❌ Participants in the market  
❌ Biased towards outcome  
❌ Unavailable after deadline  
❌ Poor communication

## Multiple Judges

Groups can have multiple judges:

### Benefits

- Backup if primary unavailable
- Specialization (sports judge, finance judge)
- Faster resolution
- Reduced bias

### Coordination

- Any authorized judge can resolve
- First resolution is final
- Admins can reassign if needed

## Technical Implementation

### Smart Contract Verification

```move
public entry fun resolve_market(
    judge: &signer,
    market_id: u64,
    outcome: u8
) {
    // Verify judge is authorized
    assert!(is_judge(judge, market_id), ERROR_NOT_JUDGE);

    // Verify market is pending
    assert!(market.status == PENDING, ERROR_NOT_PENDING);

    // Record resolution
    market.outcome = outcome;
    market.resolved_by = signer::address_of(judge);
    market.resolved_at = timestamp::now();

    // Distribute rewards
    distribute_rewards(market_id);
}
```

### Backend Validation

```typescript
async function resolveMarket(judgeId, marketId, outcome) {
  // Check: Is user a judge in this group?
  const isJudge = await checkJudgeRole(judgeId, marketId);
  if (!isJudge) throw new Error("Not authorized");

  // Check: Is market in PENDING status?
  const market = await getMarket(marketId);
  if (market.status !== "PENDING") throw new Error("Not pending");

  // Check: Has deadline passed?
  if (new Date() < market.deadline) throw new Error("Too early");

  // Proceed with on-chain transaction
  return await submitResolution(marketId, outcome);
}
```

---

**Next:** [How It Works](../How-It-Works/README.md) | [Groups](Groups.md)
