# Claiming Rewards

Complete guide to claiming your winnings from prediction markets.

## Overview

When you win a prediction, you need to **manually claim** your rewards. They don't automatically appear in your wallet.

## Quick Steps

1. Go to "My Predictions"
2. Find resolved markets where you won
3. Click "Claim Reward"
4. Approve transaction
5. ✅ MOVE tokens sent to wallet!

---

## How to Claim

### Step 1: Check Your Wins

**Navigate to My Predictions:**

1. Click "My Predictions" in sidebar
2. Filter by "Resolved"
3. Look for ✅ **WON** status

**You'll see:**

- Market title
- Your prediction (YES/NO)
- Stake amount
- **Reward amount** (your winnings!)
- Claim button

### Step 2: Click "Claim Reward"

**For each winning market:**

1. Click **"Claim Reward"** button
2. Review reward amount
3. Check gas fee (~$0.01)
4. Click "Confirm"

### Step 3: Approve Transaction

**In your wallet:**

1. Popup appears
2. Review transaction details:
   - Function: `claim_reward`
   - Market ID
   - Gas fee
3. Click "Approve"
4. Wait 5-10 seconds

### Step 4: Rewards Received!

**Confirmation:**

- ✅ Transaction successful
- Tokens added to wallet
- Button changes to "Claimed"
- Can't claim again (prevents double-claiming)

**Check your balance:**

- Wallet should show increased MOVE
- Transaction in wallet history

---

## Understanding Your Rewards

### Reward Breakdown

**What you receive:**

```
Total Reward = Your Stake + Your Profit

Example:
Stake: 5 MOVE
Profit: 3.33 MOVE
Total Reward: 8.33 MOVE
```

**Profit calculation:**

```
Your Profit = (Your Stake / Total Winning Stake) × Total Losing Stake

Example:
Your stake: 5 MOVE
Total YES (winning): 15 MOVE
Total NO (losing): 10 MOVE

Your profit: (5/15) × 10 = 3.33 MOVE
```

### Example Scenarios

#### Scenario 1: Balanced Pool

```
Market: "Will it rain tomorrow?"

Pools:
YES: 50 MOVE (you: 10 MOVE)
NO: 50 MOVE

Result: YES wins

Your reward:
= 10 + (10/50 × 50)
= 10 + 10
= 20 MOVE

ROI: +100%
```

#### Scenario 2: Underdog Victory

```
Market: "Will underdog team win?"

Pools:
YES: 20 MOVE (you: 5 MOVE)
NO: 80 MOVE

Result: YES wins (underdog!)

Your reward:
= 5 + (5/20 × 80)
= 5 + 20
= 25 MOVE

ROI: +400%!
```

#### Scenario 3: Favorite Wins

```
Market: "Will favorite team win?"

Pools:
YES: 80 MOVE (you: 10 MOVE)
NO: 20 MOVE

Result: YES wins

Your reward:
= 10 + (10/80 × 20)
= 10 + 2.5
= 12.5 MOVE

ROI: +25%
```

**Lesson:** Betting on favorites = lower returns but safer

---

## Claiming Multiple Rewards

### Batch Claiming

**If you won multiple markets:**

**Option 1: Claim One by One**

- Click each "Claim Reward" button
- Approve each transaction
- Slower but more control

**Option 2: Claim All (Planned Feature)**

- Single transaction for all wins
- Saves time and gas
- Currently not available

### Prioritize High-Value Claims

**If gas fees are a concern:**

1. Sort by reward amount
2. Claim high-value rewards first
3. Small rewards can wait
4. Batch claim when feature available

**Example:**

```
Win 1: 50 MOVE reward → Claim now
Win 2: 10 MOVE reward → Claim now
Win 3: 0.5 MOVE reward → Wait for batch claim
Win 4: 0.2 MOVE reward → Wait for batch claim
```

---

## Timing Your Claims

### When to Claim

**Immediately:**

- ✅ Need the funds for other markets
- ✅ Large reward amounts
- ✅ Want to secure profits

**Can wait:**

- ✅ Small amounts
- ✅ Saving for batch claim
- ✅ No immediate need

### Claim Deadline

**Important:** No deadline for claiming!

- Rewards locked in smart contract
- Claim anytime (even years later)
- Your rewards won't disappear
- But don't forget about them!

**Best practice:** Claim within 1 week of resolution

---

## Fees & Costs

### Gas Fees

**Per claim transaction:**

- Cost: ~$0.01 (very cheap!)
- Paid in MOVE
- Deducted from wallet balance
- NOT from your reward

**Example:**

```
Reward: 10 MOVE
Gas fee: 0.001 MOVE
You receive: 10 MOVE
You pay gas: 0.001 MOVE from wallet
Net gain: 9.999 MOVE
```

### Platform Fees

**Current:** 0% platform fee!

**Future:** May introduce small fee (e.g., 1-2%)

- Will be announced in advance
- Deducted from rewards
- Used for platform development

---

## Tracking Your Performance

### Statistics Dashboard

**View your stats:**

1. Go to "Profile" or "Stats"
2. See overall performance:
   - Total predictions
   - Win rate
   - Total earnings
   - Best prediction
   - Current streak

**Example:**

```
Total Predictions: 50
Wins: 35 (70%)
Losses: 15 (30%)
Total Staked: 100 MOVE
Total Earned: 150 MOVE
Net Profit: +50 MOVE (+50%)
Best Win: +400% on underdog bet
Current Streak: 🔥 7 wins
```

### Leaderboards

**Group Leaderboards:**

- See how you rank in your groups
- Compare with friends
- Earn badges for top positions

**Global Leaderboards:**

- Top predictors overall
- Highest win rates
- Biggest profits
- Longest streaks

---

## What If You Lost?

### Understanding Losses

**When you lose:**

- Your stake goes to winners
- No reward to claim
- Learn from the experience

**Status shows:**

- ❌ LOST
- Stake amount lost
- No claim button

### Learning from Losses

**Review what happened:**

1. Why did you predict wrong?
2. Was criteria unclear?
3. Did you research enough?
4. Was it bad luck or bad judgment?

**Improve for next time:**

- Research more thoroughly
- Read criteria carefully
- Check judge reputation
- Manage stake sizes better

### Don't Chase Losses!

**Bad strategy:**

```
Lost 5 MOVE → Bet 10 MOVE to recover
Lost 10 MOVE → Bet 20 MOVE to recover
→ Downward spiral
```

**Good strategy:**

```
Lost 5 MOVE → Accept it
Analyze what went wrong
Make smaller, smarter bets
Gradually recover
```

---

## Troubleshooting

### Can't See Claim Button

**Problem:** Won but no claim button

**Solutions:**

1. Refresh page
2. Check market is actually resolved
3. Verify you voted on winning side
4. Wait for blockchain confirmation

### Transaction Failed

**Problem:** Claim transaction failed

**Solutions:**

1. Check wallet has enough MOVE for gas
2. Try again (might be network issue)
3. Increase gas limit
4. Contact support if persists

### Wrong Reward Amount

**Problem:** Reward seems incorrect

**Check:**

1. Your stake amount
2. Total winning pool
3. Total losing pool
4. Calculation: (your stake / winning pool) × losing pool

**If still wrong:**

- Screenshot the issue
- Note market ID
- Contact support

### Already Claimed

**Problem:** Accidentally clicked claim twice

**Don't worry:**

- Smart contract prevents double-claiming
- Second transaction will fail
- You only get rewarded once
- No loss of funds

---

## Best Practices

### Claim Regularly

✅ **DO:**

- Claim within 1 week of resolution
- Keep track of pending claims
- Set reminders for large wins

❌ **DON'T:**

- Forget about small wins
- Let rewards accumulate indefinitely
- Ignore claim notifications

### Reinvest Wisely

**After claiming:**

**Option 1: Reinvest**

- Use profits for new predictions
- Compound your gains
- Grow your bankroll

**Option 2: Withdraw**

- Take profits off the table
- Secure your gains
- Reduce risk

**Option 3: Mix**

- Reinvest 50%
- Withdraw 50%
- Balanced approach

**Example:**

```
Won 10 MOVE profit

Strategy:
- Reinvest 5 MOVE in new markets
- Withdraw 5 MOVE to secure profit
- Maintain bankroll while taking gains
```

### Track Your ROI

**Calculate return on investment:**

```
ROI = (Total Earned - Total Staked) / Total Staked × 100%

Example:
Total Staked: 100 MOVE
Total Earned: 150 MOVE
ROI = (150 - 100) / 100 × 100% = +50%
```

**Good ROI targets:**

- Beginner: +10-20%
- Intermediate: +20-40%
- Advanced: +40-60%
- Expert: +60%+

---

## Tax Considerations

### Testnet Tokens

**Current (Testnet):**

- No real value
- No tax implications
- Just for testing

### Mainnet (Future)

**When on mainnet:**

- Winnings may be taxable
- Depends on your jurisdiction
- Keep records of:
  - Stakes
  - Wins
  - Losses
  - Dates
- Consult tax professional

**Not financial or tax advice!**

---

## Next Steps

**Claimed your rewards?**

**Reinvest:**

- [Browse Markets](https://predictly-labs.vercel.app/explore)
- [Create Market](Creating-Markets.md)
- [Voting Guide](Voting-Guide.md)

**Learn more:**

- [Products Overview](../Products/README.md)
- [Groups](../Products/Groups.md)
- [Judge System](../Products/Judge-System.md)

**Need help?** [Troubleshooting](../Resources/Troubleshooting.md)

---

## Summary

**Remember:**

1. ✅ Check "My Predictions" for wins
2. ✅ Click "Claim Reward" button
3. ✅ Approve transaction in wallet
4. ✅ Rewards sent to wallet
5. ✅ Track your performance
6. ✅ Reinvest or withdraw wisely

**Congratulations on your win! 🎉**
