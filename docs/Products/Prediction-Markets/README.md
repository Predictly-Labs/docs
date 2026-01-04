# Prediction Markets

## Overview

Prediction markets are the core product of Predictly. Create YES/NO markets on any topic and stake MOVE tokens on your prediction.

## What is a Prediction Market?

A prediction market is a platform where users can bet on the outcome of future events. Unlike traditional betting:

- **Decentralized** - No central authority controls outcomes
- **Transparent** - All bets recorded on blockchain
- **Fair** - Judges from your own community
- **Social** - Compete with friends, not strangers

## Market Types

Predictly offers three types of markets:

### 1. Full Degen Markets

[View Details →](Full-Degen.md)

### 2. Zero Loss Markets

[View Details →](Zero-Loss.md)

### 3. Private Markets

[View Details →](Private.md)

## How Markets Work

### 1. Creation

Anyone can create a market for free (no gas fees).

```
Title: "Will Bitcoin reach $100k by end of 2025?"
Description: Bitcoin must reach or exceed $100,000 USD
Deadline: 2025-12-31 23:59 UTC
Group: Public or specific group
```

### 2. Betting Phase

Users stake MOVE tokens on YES or NO.

```
YES Pool: 50 MOVE (60%)
NO Pool: 33.33 MOVE (40%)
Total: 83.33 MOVE
Participants: 15
```

### 3. Resolution

After deadline, judge determines outcome based on real-world result.

### 4. Payout

Winners claim their share of the losing pool proportionally.

## Reward Calculation

```
Your Reward = (Your Stake / Total Winning Stake) × Total Losing Stake
```

**Example:**

- You voted YES: 10 MOVE
- Total YES: 50 MOVE
- Total NO: 33.33 MOVE
- Result: YES wins
- Your reward: (10/50) × 33.33 = 6.67 MOVE
- You receive: 10 + 6.67 = **16.67 MOVE**

## Market Parameters

| Parameter   | Description        | Default  |
| ----------- | ------------------ | -------- |
| Title       | Market question    | Required |
| Description | Detailed criteria  | Optional |
| Deadline    | When voting closes | Required |
| Min Stake   | Minimum bet amount | 0.1 MOVE |
| Max Stake   | Maximum bet amount | None     |
| Group       | Public or private  | Public   |

## Market Status

| Status    | Description                        |
| --------- | ---------------------------------- |
| ACTIVE    | Betting is open                    |
| PENDING   | Deadline passed, waiting for judge |
| RESOLVED  | Judge has decided outcome          |
| DISPUTED  | Resolution is being contested      |
| CANCELLED | Market cancelled, refunds issued   |

## Market Outcomes

| Outcome | Effect                        |
| ------- | ----------------------------- |
| YES     | YES voters win, split NO pool |
| NO      | NO voters win, split YES pool |
| INVALID | Everyone refunded, no winners |

---

**Next:** [Full Degen Markets](Full-Degen.md) | [Groups](../Groups.md)
