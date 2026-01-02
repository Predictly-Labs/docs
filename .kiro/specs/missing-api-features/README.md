# Missing API Features Specification

## Overview

This specification covers the implementation of 9 missing API endpoints and features for the Predictly backend, identified during brainstorming session on December 30, 2024.

## Problem Statement

The current backend API is missing several critical endpoints that prevent users from:
1. Easily viewing their groups
2. Efficiently browsing their prediction history
3. Understanding their performance statistics
4. Managing judges and group settings

## Solution

Implement missing endpoints in 3 sprints:
- **Sprint 1 (Critical):** My Groups, My Votes Pagination, Check My Vote
- **Sprint 2 (High Priority):** Role Filtering, Statistics, Market Type Filter
- **Sprint 3 (Polish):** Group Settings, Judge History, Bulk Assignment

## Documents

- **[requirements.md](requirements.md)** - Detailed requirements with acceptance criteria
- **[design.md](design.md)** - Technical design and architecture
- **[tasks.md](tasks.md)** - Implementation tasks organized by sprint

## Key Features

### Sprint 1: Critical (Week 1)
1. **My Groups Endpoint** - `GET /api/groups/my-groups`
   - List all groups user is member of
   - Filter by role, search, sort
   - Pagination support

2. **My Votes Pagination** - Enhanced `GET /api/predictions/my-votes`
   - Add pagination (page, limit)
   - Filter by status, group, outcome
   - Maintain backward compatibility

3. **Check My Vote** - `GET /api/predictions/:marketId/my-vote`
   - Check if user voted on specific market
   - Return vote details or null

### Sprint 2: High Priority (Week 2)
4. **Role Filter** - `GET /api/groups/:id/members?role=JUDGE`
   - Filter group members by role
   - Show judges, admins, etc.

5. **Vote Statistics** - `GET /api/predictions/my-votes/stats`
   - Aggregate statistics (ROI, win rate)
   - Group-by-group breakdown
   - Performance metrics

6. **Market Type Filter** - `GET /api/markets?marketType=NO_LOSS`
   - Filter markets by type
   - Support STANDARD, NO_LOSS, WITH_YIELD

### Sprint 3: Polish (Week 3)
7. **Group Settings** - `GET/PUT /api/groups/:id/settings`
   - Default market type per group
   - Allowed market types configuration
   - Admin-only modification

8. **Judge History** - `GET /api/users/:userId/resolved-markets`
   - View markets resolved by judge
   - Transparency and accountability

9. **Bulk Judge Assignment** - `POST /api/groups/:groupId/judges/bulk`
   - Assign multiple judges at once
   - Easier group setup

## Timeline

- **Sprint 1:** 5-7 days (Critical features)
- **Sprint 2:** 4-5 days (High priority)
- **Sprint 3:** 5-6 days (Polish + migration)

**Total:** ~3 weeks for complete implementation

## Success Criteria

### Sprint 1:
- ✅ Users can see their groups easily
- ✅ Pagination works for vote history
- ✅ Can check if voted on market

### Sprint 2:
- ✅ Can filter judges in group
- ✅ Statistics display correctly
- ✅ Market type filter works

### Sprint 3:
- ✅ Group settings persist
- ✅ Judge history displays
- ✅ Bulk assignment works

## Technical Details

### Database Changes
- **Sprint 3 Only:** Add `defaultMarketType` and `allowedMarketTypes` to Group model
- **Sprint 3 Only:** Add performance indexes

### API Changes
- All changes are additive (no breaking changes)
- Existing endpoints enhanced with optional parameters
- New endpoints follow existing patterns

### Testing
- Unit tests for all controllers
- Property-based tests for correctness properties
- Integration tests for complete flows
- Performance tests for large datasets

## Dependencies

- Node.js 18+
- PostgreSQL
- Prisma ORM
- Express.js
- Zod (validation)

## Getting Started

1. Review [requirements.md](requirements.md) for detailed requirements
2. Review [design.md](design.md) for technical design
3. Follow [tasks.md](tasks.md) for implementation steps
4. Start with Sprint 1 (Critical features)

## Related Documentation

- [Backend README](../../backend/README.md)
- [API Documentation](../../backend/docs/API_DOCUMENTATION.md)
- [Rate Limiting](../../backend/docs/RATE_LIMITING.md)
- [Deployment Checklist](../../backend/docs/DEPLOYMENT_CHECKLIST.md)

## Questions?

For implementation questions, refer to:
- [MISSING_FEATURES_PLAN.md](../../backend/docs/MISSING_FEATURES_PLAN.md) - Detailed implementation guide
- [MISSING_FEATURES_QUICK_REF.md](../../backend/docs/MISSING_FEATURES_QUICK_REF.md) - Quick reference

