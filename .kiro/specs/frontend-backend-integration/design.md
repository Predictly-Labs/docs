# Design Document

## Overview

This design outlines the integration between the Predictly Next.js frontend and Express.js backend. The integration uses a centralized API client, React Context for state management, and custom hooks for data fetching.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Next.js Frontend                        │
├─────────────────────────────────────────────────────────────┤
│  Pages/Components                                           │
│  ├── Landing Page (public)                                  │
│  ├── Dashboard (authenticated)                              │
│  ├── Groups (list, detail, create)                          │
│  ├── Predictions (list, detail, vote)                       │
│  └── Rewards (history, claim)                               │
├─────────────────────────────────────────────────────────────┤
│  Hooks Layer                                                │
│  ├── useAuth() - authentication state                       │
│  ├── useGroups() - group operations                         │
│  ├── usePredictions() - market operations                   │
│  └── useUser() - profile operations                         │
├─────────────────────────────────────────────────────────────┤
│  Context Layer                                              │
│  ├── AuthContext - user session, token                      │
│  └── PrivyProvider - wallet connection                      │
├─────────────────────────────────────────────────────────────┤
│  API Client (lib/api.ts)                                    │
│  └── Axios instance with interceptors                       │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                   Express.js Backend                        │
│                https://backend-3ufs.onrender.com            │
└─────────────────────────────────────────────────────────────┘
```

## Components and Interfaces

### API Client

```typescript
// lib/api.ts
import axios from 'axios';

const api = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_URL,
  headers: { 'Content-Type': 'application/json' },
});

// Request interceptor - add auth token
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Response interceptor - handle 401
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      localStorage.removeItem('token');
      window.location.href = '/';
    }
    return Promise.reject(error);
  }
);

export default api;
```

### Auth Context

```typescript
// contexts/AuthContext.tsx
interface AuthContextType {
  user: User | null;
  token: string | null;
  isLoading: boolean;
  login: (privyId: string, walletAddress?: string) => Promise<void>;
  logout: () => void;
}
```

### API Service Functions

```typescript
// lib/api/auth.ts
export const authApi = {
  login: (data: LoginInput) => api.post('/api/users/auth/privy', data),
  getMe: () => api.get('/api/users/me'),
  updateProfile: (data: UpdateProfileInput) => api.put('/api/users/me', data),
};

// lib/api/groups.ts
export const groupsApi = {
  list: (params?: ListParams) => api.get('/api/groups', { params }),
  getById: (id: string) => api.get(`/api/groups/${id}`),
  create: (data: CreateGroupInput) => api.post('/api/groups', data),
  join: (inviteCode: string) => api.post('/api/groups/join', { inviteCode }),
};

// lib/api/predictions.ts
export const predictionsApi = {
  list: (params?: ListParams) => api.get('/api/predictions', { params }),
  getById: (id: string) => api.get(`/api/predictions/${id}`),
  create: (data: CreateMarketInput) => api.post('/api/predictions', data),
  vote: (id: string, data: VoteInput) => api.post(`/api/predictions/${id}/vote`, data),
  resolve: (id: string, data: ResolveInput) => api.post(`/api/predictions/${id}/resolve`, data),
  claim: (id: string) => api.post(`/api/predictions/${id}/claim`),
  getMyVotes: () => api.get('/api/predictions/my-votes'),
};
```

## Data Models

### TypeScript Types

```typescript
// types/api.ts
interface User {
  id: string;
  privyId: string;
  walletAddress?: string;
  displayName?: string;
  avatarUrl?: string;
  totalPredictions: number;
  correctPredictions: number;
  totalEarnings: number;
  isPro: boolean;
}

interface Group {
  id: string;
  name: string;
  description?: string;
  iconUrl?: string;
  inviteCode: string;
  isPublic: boolean;
  memberCount: number;
  marketCount: number;
}

interface PredictionMarket {
  id: string;
  groupId: string;
  title: string;
  description?: string;
  imageUrl?: string;
  status: 'ACTIVE' | 'PENDING' | 'RESOLVED' | 'DISPUTED' | 'CANCELLED';
  outcome?: 'YES' | 'NO' | 'INVALID';
  yesPercentage: number;
  noPercentage: number;
  yesPool: number;
  noPool: number;
  totalVolume: number;
  participantCount: number;
  endDate: string;
  minStake: number;
  maxStake?: number;
}

interface Vote {
  id: string;
  marketId: string;
  prediction: 'YES' | 'NO';
  amount: number;
  rewardAmount?: number;
  hasClaimedReward: boolean;
  mockYield: number;
  market: PredictionMarket;
}

interface ApiResponse<T> {
  success: boolean;
  data: T;
  message?: string;
  meta?: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}
```

## Error Handling

```typescript
// lib/api/error.ts
export class ApiError extends Error {
  constructor(
    public status: number,
    public message: string,
    public errors?: Record<string, string[]>
  ) {
    super(message);
  }
}

export function handleApiError(error: unknown): ApiError {
  if (axios.isAxiosError(error)) {
    return new ApiError(
      error.response?.status || 500,
      error.response?.data?.message || 'An error occurred',
      error.response?.data?.errors
    );
  }
  return new ApiError(500, 'An unexpected error occurred');
}
```

## Testing Strategy

### Unit Tests
- Test API client interceptors
- Test auth context state management
- Test custom hooks with mock API responses

### Integration Tests
- Test full auth flow (Privy -> Backend -> Token storage)
- Test group creation and joining flow
- Test prediction voting flow
