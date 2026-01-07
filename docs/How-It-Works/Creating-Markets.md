# Creating Markets

Learn how to create your own prediction markets on Predictly.

## Overview

Anyone can create prediction markets for **FREE** (no gas fees). The backend pays the gas for market initialization.

## Before You Start

### Requirements

- ✅ Connected wallet
- ✅ Member of a group (for private markets)
- ✅ Clear prediction question
- ✅ Verifiable outcome criteria

### Market Types

| Type           | Access         | Risk   | Best For                    |
| -------------- | -------------- | ------ | --------------------------- |
| **Full Degen** | Public/Private | High   | High conviction predictions |
| **Zero Loss**  | Public/Private | None   | Risk-averse users (Live)    |
| **Private**    | Group only     | Varies | Friend group predictions    |

---

## Step-by-Step Guide

<figure><img src="../.gitbook/assets/image (1) (1).png" alt="" width="260"><figcaption><p>Popup option create prediction market</p></figcaption></figure>

### 1. Click "Create Market"

- From dashboard: Click **"+ Create Market"**
- From group page: Click **"New Market"**

### 2. Fill in Basic Information

#### Title

The main prediction question.

**Good examples:**

```
✅ "Will Bitcoin reach $100k by Dec 31, 2026?"
✅ "Will it rain in Jakarta tomorrow?"
✅ "Will I finish my thesis by end of month?"
```

**Bad examples:**

```
❌ "Bitcoin price" (not a yes/no question)
❌ "Weather" (too vague)
❌ "Will I be happy?" (not verifiable)
```

**Tips:**

- Keep it clear and concise
- Make it a YES/NO question
- Include timeframe
- Be specific

#### Description

Detailed resolution criteria.

**Example:**

```
Bitcoin must reach or exceed $100,000 USD on any major exchange
(Binance, Coinbase, Kraken) before 23:59 UTC on December 31, 2026.

Price will be verified using CoinGecko data.
Judge will check official exchange records.
```

**Include:**

- Exact criteria for YES
- Exact criteria for NO
- Data sources
- How judge will verify

### 3. Set Deadline

When voting closes.

**Examples:**

- Short-term: Tomorrow, next week
- Medium-term: Next month, end of quarter
- Long-term: End of year, next year

**Tips:**

- Give enough time for participation
- Not too far (people lose interest)
- Consider verification time needed
- Account for timezone (use UTC)

### 4. Choose Market Type

#### Full Degen (Standard)

- Winners take full losing pool
- High risk, high reward
- Most popular

#### Zero Loss (Live on Testnet)

- Stakes earn yield
- Only yield distributed
- Principal protected

#### Private Market

- Only group members can see/vote
- Great for personal predictions
- Requires group membership

### 5. Set Parameters (Optional)

#### Minimum Stake

Minimum amount users can bet.

- **Default:** 0.1 MOVE
- **Range:** 0.01 - 10 MOVE
- **Tip:** Lower = more accessible

#### Maximum Stake

Maximum amount users can bet.

- **Default:** No limit
- **Use case:** Prevent whales dominating
- **Example:** 10 MOVE max for fair play

### 6. Upload Image (Optional)

Add visual appeal to your market.

**Supported formats:**

- JPG, PNG, GIF
- Max size: 5MB
- Recommended: 1200x630px

**Tips:**

- Use relevant images
- Clear and professional
- No copyrighted content
- Compress for faster loading

**Image is stored on IPFS** (decentralized storage)

### 7. Select Group (For Private Markets)

Choose which group can access this market.

- Only group members can see
- Only group members can vote
- Group judge will resolve

### 8. Review & Create

**Check everything:**

- ✅ Title is clear
- ✅ Description is detailed
- ✅ Deadline is correct
- ✅ Parameters are set
- ✅ Image uploaded (optional)

**Click "Create Market"**

### 9. Market Created!

**What happens:**

1. Market saved to database (instant)
2. Market gets unique ID
3. Backend initializes on-chain (15-30 seconds)
4. Market becomes ACTIVE
5. Users can start voting!

**You'll see:**

- Market ID
- Share link
- Invite code (for private)

---

## Best Practices

### Clear Criteria

**Good:**

```
Market: "Will Team A win the championship?"

Criteria:
- Team A must be declared official champion
- Tournament must complete by deadline
- Judge will verify from official tournament website
- If tournament cancelled = INVALID
```

**Bad:**

```
Market: "Will Team A do well?"

Criteria:
- They should play good
```

### Verifiable Outcomes

**Easy to verify:**

- ✅ Sports results (official records)
- ✅ Weather data (weather.com)
- ✅ Stock prices (Yahoo Finance)
- ✅ Personal goals (photo proof)

**Hard to verify:**

- ❌ Subjective opinions
- ❌ Private information
- ❌ Unprovable claims
- ❌ Ambiguous criteria

### Fair Deadlines

**Good:**

```
Created: Jan 1, 2026
Deadline: Dec 31, 2026
Duration: 12 months
Reason: Long-term prediction needs time
```

**Bad:**

```
Created: Jan 1, 2026
Deadline: Jan 2, 2026
Duration: 1 day
Reason: Not enough time to participate
```

### Appropriate Stakes

**For friend groups:**

- Min: 0.1 MOVE
- Max: 5 MOVE
- Keeps it fun, not stressful

**For public markets:**

- Min: 0.5 MOVE
- Max: No limit
- Let market decide

---

## Market Examples

### Example 1: Sports Prediction

```
Title: "Will Manchester United win the Premier League 2025/26?"

Description:
Manchester United must be crowned Premier League champions
for the 2025/26 season.

Resolution criteria:
- Official Premier League announcement
- Season must complete by May 31, 2026
- If season cancelled = INVALID
- Judge will verify from premierleague.com

Deadline: May 31, 2026
Type: Full Degen
Min Stake: 0.5 MOVE
Group: Public
```

### Example 2: Personal Goal

```
Title: "Will I lose 5kg by end of month?"

Description:
I must lose at least 5kg from current weight (75kg)
by the last day of this month.

Resolution criteria:
- Starting weight: 75kg (verified Jan 1)
- Target weight: 70kg or less
- Judge will verify with scale photo
- Must be same scale, same time of day
- If I don't provide proof = NO

Deadline: Jan 31, 2026
Type: Private
Min Stake: 0.1 MOVE
Max Stake: 2 MOVE
Group: Kos Squad
```

### Example 3: Crypto Price

```
Title: "Will ETH reach $5000 by Q1 2026?"

Description:
Ethereum (ETH) must reach or exceed $5,000 USD on any
major exchange before March 31, 2026 23:59 UTC.

Resolution criteria:
- Price source: CoinGecko
- Exchanges: Binance, Coinbase, Kraken
- Must hit $5000 even for 1 second
- Judge will check historical data
- Screenshot proof required

Deadline: Mar 31, 2026
Type: Full Degen
Min Stake: 1 MOVE
Group: Public
```

---

## After Creation

### Share Your Market

**Get the link:**

1. Click "Share" button
2. Copy link
3. Share on:
   - Twitter/X
   - Discord
   - Telegram
   - WhatsApp

**Invite code (private markets):**

- Share with group members only
- Code is unique per market
- Anyone with code can join group

### Monitor Activity

**Track:**

- Number of votes
- Total pool size
- YES/NO percentages
- Participant list

**Update:**

- Can't edit after creation
- If mistake: Cancel and recreate
- Add clarifications in comments

### Assign Judge (Group Admins)

If you're group admin:

1. Go to group settings
2. Assign trusted members as judges
3. Judges can resolve all group markets

---

## Troubleshooting

### Market Not Appearing

**Problem:** Created market doesn't show up

**Solutions:**

1. Wait 30 seconds for on-chain initialization
2. Refresh page
3. Check "My Markets" tab
4. Verify transaction succeeded

### Can't Create Market

**Problem:** Create button disabled

**Solutions:**

1. Connect wallet
2. Join a group (for private markets)
3. Check internet connection
4. Try different browser

### Image Upload Failed

**Problem:** Image won't upload

**Solutions:**

1. Check file size (<5MB)
2. Use JPG or PNG format
3. Try smaller image
4. Skip image (optional anyway)

---

## Tips for Success

### Engage Participants

- Share on social media
- Explain your reasoning
- Respond to questions
- Build hype!

### Set Realistic Deadlines

- Not too short (need participation)
- Not too long (people forget)
- Consider verification time

### Choose Good Judges

- Trustworthy
- Knowledgeable about topic
- Available after deadline
- Objective

### Start Small

- Create simple markets first
- Learn what works
- Build reputation
- Scale up gradually

---

## Next Steps

**Market created?** Learn how to:

- [Vote on Markets](Voting-Guide.md)
- [Claim Rewards](Claiming-Rewards.md)
- [Manage Groups](../Products/Groups.md)

**Need help?** Check [Troubleshooting](../Resources/Troubleshooting.md)
