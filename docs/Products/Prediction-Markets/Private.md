# Private Markets

## Overview

Private Markets are **group-exclusive** prediction markets. Only members of a specific group can participate.

## How It Works

### 1. Group Admin Creates Market

Admin or members create a market within their group.

```
Group: "Kos Squad"
Market: "Will Bang Isal finish thesis by end of month?"
Access: Only Kos Squad members
```

### 2. Group Members Vote

Only group members can see and vote on the market.

### 3. Group Judge Resolves

A judge from the same group resolves the market.

### 4. Group-Only Payouts

Rewards distributed among group members only.

## Example Scenario

**Group:** Kos Squad (5 members)

**Market:** "Who will lose the most weight this month?"

**Participants:**

```
Entry Fee: 2 MOVE per person
Total Pool: 10 MOVE

Participants:
├── Bang Isal: 2 MOVE
├── Reza: 2 MOVE
├── Dika: 2 MOVE
├── Ayu: 2 MOVE
└── Budi: 2 MOVE
```

**Resolution:**

```
Judge (Reza) verifies:
- Bang Isal: Lost 5kg ← WINNER
- Dika: Lost 3kg
- Others: <2kg

Payout:
Bang Isal receives: 10 MOVE (5x return!)
```

## Benefits

### Privacy

- Markets not visible to public
- Only group members can participate
- Internal group matters stay internal

### Trust

- Judge is someone you know
- Participants are your friends
- Social accountability

### Customization

- Group-specific topics
- Custom rules
- Flexible resolution criteria

## Use Cases

### Personal Goals

```
"Will I complete my thesis by deadline?"
- Group holds you accountable
- Small stakes make it meaningful
- Friends support your goal
```

### Group Challenges

```
"Monthly fitness challenge"
- Everyone stakes equally
- Judge verifies progress
- Winner takes pool
```

### Friendly Bets

```
"Who will get promoted first?"
- Office friends compete
- Judge confirms official news
- Bragging rights + rewards
```

### Study Groups

```
"Who will score highest on exam?"
- Study group competition
- Judge verifies scores
- Motivates everyone to study harder
```

## Creating Private Markets

### Requirements

- Must be a group member
- Group must have assigned judge
- Follow group rules

### Steps

1. Go to your group page
2. Click "Create Market"
3. Fill in details
4. Select "Private" visibility
5. Submit (FREE, no gas)

### Best Practices

**Clear Criteria**

```
✅ "Lose 5kg by end of month (verified with scale photo)"
❌ "Get healthier this month"
```

**Fair Deadlines**

```
✅ 30 days for weight loss challenge
❌ 3 days for major goal
```

**Trusted Judges**

```
✅ Friend who can verify objectively
❌ Participant with conflict of interest
```

## Privacy & Security

### What's Private

- Market details
- Participants
- Vote amounts
- Results

### What's Public

- Group name (if group is public)
- Number of markets (stats only)
- Judge reputation

### Data Storage

- Market data: On-chain (encrypted)
- Participant info: Off-chain database
- Images: IPFS (private links)

## Group Roles

| Role          | Permissions                                   |
| ------------- | --------------------------------------------- |
| **Admin**     | Create markets, manage members, assign judges |
| **Judge**     | Resolve markets, verify outcomes              |
| **Moderator** | Remove inappropriate markets                  |
| **Member**    | Create & vote on markets                      |

## Fees

| Action                | Cost             |
| --------------------- | ---------------- |
| Create Private Market | FREE             |
| Vote                  | Gas fee (~$0.01) |
| Claim Reward          | Gas fee (~$0.01) |

## Limits

| Limit                        | Value    |
| ---------------------------- | -------- |
| Max members per group        | 100      |
| Max active markets per group | 50       |
| Min stake                    | 0.1 MOVE |
| Max stake                    | No limit |

## Tips

### For Admins

1. Assign multiple judges for backup
2. Set clear group rules
3. Remove inactive members
4. Moderate inappropriate markets

### For Members

1. Read market criteria carefully
2. Trust but verify judge decisions
3. Keep stakes reasonable
4. Have fun!

### For Judges

1. Be objective and fair
2. Document your verification
3. Resolve promptly after deadline
4. Communicate with participants

---

**Next:** [Groups & Communities](../Groups.md) | [Judge System](../Judge-System.md)
