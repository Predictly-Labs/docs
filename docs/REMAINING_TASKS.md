# Remaining Documentation Tasks

Daftar dokumentasi yang belum selesai dan perlu dibuat untuk melengkapi GitBook Predictly-Labs.

---

## 🔴 High Priority

### 1. Backend API Documentation (11 files)

**Location:** `docs/developers/backend-api/`

| File                       | Description                    | Source                                 |
| -------------------------- | ------------------------------ | -------------------------------------- |
| `overview.md`              | Backend overview, tech stack   | `backend/README.md`                    |
| `authentication.md`        | Wallet auth flow, JWT          | `backend/README.md`                    |
| `endpoints/README.md`      | Endpoints overview             | New                                    |
| `endpoints/health.md`      | Health check endpoint          | `backend/README.md`                    |
| `endpoints/auth.md`        | Auth endpoints                 | `backend/README.md`                    |
| `endpoints/users.md`       | User endpoints                 | `backend/README.md`                    |
| `endpoints/groups.md`      | Group endpoints                | `backend/README.md`                    |
| `endpoints/markets.md`     | Market endpoints               | `backend/README.md`                    |
| `endpoints/predictions.md` | Prediction endpoints           | `backend/README.md`                    |
| `endpoints/contract.md`    | Contract endpoints             | `backend/README.md`                    |
| `endpoints/admin.md`       | Admin endpoints                | `backend/README.md`                    |
| `hybrid-flow.md`           | Hybrid on-chain/off-chain flow | `backend/README.md`                    |
| `rate-limiting.md`         | Rate limit config              | `backend/docs/RATE_LIMITING.md`        |
| `deployment.md`            | Backend deployment             | `backend/docs/DEPLOYMENT_CHECKLIST.md` |

**Notes:**

- Migrate dari `backend/README.md`
- Split endpoints per category
- Add request/response examples

---

### 2. User Guides (5 files)

**Location:** `docs/guides-tutorials/user-guides/`

| File               | Description          | Content                    |
| ------------------ | -------------------- | -------------------------- |
| `README.md`        | User guides overview | Index page                 |
| `create-market.md` | How to create market | Step-by-step + screenshots |
| `place-vote.md`    | How to vote          | Step-by-step + screenshots |
| `claim-rewards.md` | How to claim         | Step-by-step + screenshots |
| `manage-group.md`  | Manage your group    | Step-by-step + screenshots |

**Notes:**

- Need screenshots dari aplikasi
- Step-by-step dengan gambar
- Tips dan troubleshooting

---

### 3. Getting Started (3 files remaining)

**Location:** `docs/getting-started/`

| File                | Description            | Status |
| ------------------- | ---------------------- | ------ |
| `getting-tokens.md` | How to get MOVE tokens | New    |
| `first-market.md`   | Create first market    | New    |
| `joining-groups.md` | Join/create groups     | New    |

---

## 🟡 Medium Priority

### 4. How It Works (6 files)

**Location:** `docs/how-it-works/`

| File                     | Description                   | Content               |
| ------------------------ | ----------------------------- | --------------------- |
| `system-architecture.md` | Architecture overview         | Diagram + explanation |
| `market-lifecycle.md`    | Market from creation to claim | Flowchart             |
| `voting-mechanism.md`    | How voting works              | Technical details     |
| `resolution-process.md`  | Judge resolution flow         | Step-by-step          |
| `reward-distribution.md` | Reward calculation            | Math + examples       |
| `hybrid-flow.md`         | On-chain/off-chain flow       | Sequence diagram      |

**Notes:**

- Butuh Mermaid diagrams
- Technical but accessible

---

### 5. Products & Features (9 files)

**Location:** `docs/products-features/`

| File                                    | Description             |
| --------------------------------------- | ----------------------- |
| `prediction-markets/overview.md`        | Market types overview   |
| `prediction-markets/full-degen.md`      | Standard markets        |
| `prediction-markets/zero-loss.md`       | Protected markets       |
| `prediction-markets/private-markets.md` | Private group markets   |
| `groups.md`                             | Groups feature          |
| `judge-system.md`                       | Judge role & resolution |
| `wallet-integration.md`                 | Multi-wallet support    |
| `analytics.md`                          | Analytics dashboard     |

---

### 6. Architecture & Frontend (7 files)

**Location:** `docs/developers/`

| File                            | Description          |
| ------------------------------- | -------------------- |
| `architecture/overview.md`      | System architecture  |
| `architecture/tech-stack.md`    | Technology breakdown |
| `architecture/hybrid-system.md` | Hybrid architecture  |
| `frontend/setup.md`             | Frontend setup       |
| `frontend/wallet-connection.md` | Wallet integration   |
| `frontend/api-integration.md`   | API usage            |
| `frontend/state-management.md`  | State management     |

---

## 🟢 Low Priority

### 7. FAQ (3 files)

**Location:** `docs/faq/`

| File                 | Description         |
| -------------------- | ------------------- |
| `general.md`         | General questions   |
| `technical.md`       | Technical questions |
| `troubleshooting.md` | Common issues       |

---

### 8. Community (4 files)

**Location:** `docs/community/`

| File              | Description       |
| ----------------- | ----------------- |
| `contributing.md` | How to contribute |
| `discord.md`      | Discord community |
| `github.md`       | GitHub links      |
| `contact.md`      | Contact info      |

---

### 9. Security (3 files)

**Location:** `docs/security/`

| File                         | Description        |
| ---------------------------- | ------------------ |
| `smart-contract-security.md` | Contract security  |
| `api-security.md`            | API security       |
| `best-practices.md`          | User security tips |

---

### 10. Testing (3 files)

**Location:** `docs/developers/testing/`

| File                     | Description              |
| ------------------------ | ------------------------ |
| `smart-contracts.md`     | Contract testing         |
| `api-testing.md`         | API testing with Postman |
| `integration-testing.md` | E2E testing              |

---

### 11. Developer Tutorials (4 files)

**Location:** `docs/guides-tutorials/developer-tutorials/`

| File                      | Description              |
| ------------------------- | ------------------------ |
| `README.md`               | Tutorial overview        |
| `building-with-api.md`    | Build with Predictly API |
| `contract-integration.md` | Integrate contracts      |
| `custom-frontend.md`      | Build custom frontend    |

---

### 12. Remaining Deployments (3 files)

**Location:** `docs/deployments/`

| File               | Description      |
| ------------------ | ---------------- |
| `network-info.md`  | Network details  |
| `backend-urls.md`  | Backend API URLs |
| `frontend-urls.md` | Frontend URLs    |

---

### 13. Mission & Vision (3 files)

**Location:** `docs/mission-vision/`

| File                      | Description          |
| ------------------------- | -------------------- |
| `our-mission.md`          | Project mission      |
| `why-decentralized.md`    | Why decentralization |
| `community-governance.md` | Governance model     |

---

## 📊 Summary

| Priority  | Section                 | Files | Est. Time |
| --------- | ----------------------- | ----- | --------- |
| 🔴 High   | Backend API             | 14    | 3-4 hours |
| 🔴 High   | User Guides             | 5     | 2-3 hours |
| 🔴 High   | Getting Started         | 3     | 1 hour    |
| 🟡 Medium | How It Works            | 6     | 2-3 hours |
| 🟡 Medium | Products & Features     | 9     | 2-3 hours |
| 🟡 Medium | Architecture & Frontend | 7     | 2 hours   |
| 🟢 Low    | FAQ                     | 3     | 1 hour    |
| 🟢 Low    | Community               | 4     | 30 min    |
| 🟢 Low    | Security                | 3     | 1 hour    |
| 🟢 Low    | Testing                 | 3     | 1 hour    |
| 🟢 Low    | Developer Tutorials     | 4     | 2 hours   |
| 🟢 Low    | Deployments             | 3     | 30 min    |
| 🟢 Low    | Mission & Vision        | 3     | 1 hour    |

**Total Remaining: ~67 files, ~20 hours**

---

## ✅ Already Completed

- `README.md` - Home page
- `SUMMARY.md` - Navigation
- `introduction/` - 4 files (100%)
- `developers/smart-contracts/` - 5 files (100%)
- `getting-started/quick-start.md`
- `getting-started/wallet-setup.md`
- `deployments/contract-addresses.md`

**Completed: 14 files**

---

## 📝 Template for New Pages

```markdown
# Page Title

Brief introduction about what this page covers.

## Section 1

Content here...

### Subsection

More details...

## Section 2

Content here...

## Examples

\`\`\`typescript
// Code example
\`\`\`

## Next Steps

- [Link to related page](../path/to/page.md)
- [Another related page](../path/to/another.md)

---

**Related:**

- [Previous →](previous.md)
- [Next →](next.md)
```

---

## 🔗 Quick Commands

**Create Backend API Overview:**

```bash
# Copy and edit from backend README
cp backend/README.md docs/developers/backend-api/overview.md
```

**Create Empty Files:**

```powershell
# PowerShell - create multiple files
@("general.md", "technical.md", "troubleshooting.md") | ForEach-Object {
  New-Item -Path "docs/faq/$_" -Force
}
```

---

**Last Updated:** 2026-01-03
**Status:** In Progress
