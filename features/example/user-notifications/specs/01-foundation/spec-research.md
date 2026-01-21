# Spec Research: Foundation

**Date:** 2025-01-05
**Author:** @example-team
**Spec:** `01-foundation`

---

## Problem Context

Users have no way to receive updates about important events. They must manually check for changes, leading to missed information and poor engagement. This spec covers the core notification service and UI components.

---

## Exploration Findings

### Existing Patterns

| Pattern | Used In | Notes |
|---------|---------|-------|
| API Client | `src/api/client.ts` | get/patch methods, async/await |
| Context + useReducer | `src/contexts/AuthContext.tsx` | Global state pattern |
| Functional components | `src/components/` | Hooks-based with CSS modules |

### Key Files

| File | Relevance |
|------|-----------|
| `src/api/client.ts` | Follow existing API patterns |
| `src/components/Header.tsx` | Integration point for NotificationBell |
| `src/contexts/AuthContext.tsx` | Pattern for context setup |
| `src/hooks/usePolling.ts` | May exist, check first |

### Integration Points

- Header.tsx line ~45 - NotificationBell placement before UserMenu
- Existing API client for backend calls
- React Context for cross-component state

---

## Key Decisions

### Decision 1: Polling vs WebSocket

**Options considered:**
1. WebSocket - Real-time, complex infrastructure
2. Polling (30s) - Simple, acceptable latency

**Chosen:** Polling (30-second interval)

**Rationale:** Simpler for v1, no backend WebSocket infrastructure needed, acceptable latency for notifications. Can upgrade later.

### Decision 2: State Management

**Options considered:**
1. Redux - Full featured, heavy
2. React Context + useReducer - Lightweight, already used

**Chosen:** React Context + useReducer

**Rationale:** Notifications needed across components, avoids prop drilling, pattern already exists in codebase.

### Decision 3: Optimistic Updates

**Options considered:**
1. Wait for server response
2. Optimistic update with revert on error

**Chosen:** Optimistic updates for mark-as-read

**Rationale:** Better UX with immediate feedback. Low risk - worst case notification shows as unread again.

---

## Interface Contracts

### Types

```typescript
// src/notifications/types.ts

export type NotificationType = 'info' | 'warning' | 'action-required';

export interface Notification {
  id: string;
  type: NotificationType;
  message: string;
  timestamp: string;  // ISO 8601
  read: boolean;
  actionUrl?: string; // Optional link
}

export interface NotificationState {
  notifications: Notification[];
  unreadCount: number;
  isLoading: boolean;
  error: string | null;
}
```

### Function Contracts

| Function | Signature | Responsibility | Dependencies |
|----------|-----------|----------------|--------------|
| `fetchNotifications` | `() => Promise<NotificationState>` | Fetch from API, transform response | apiClient |
| `markAsRead` | `(id: string) => Promise<void>` | Update single notification | apiClient |
| `markAllAsRead` | `() => Promise<number>` | Batch update, return count | apiClient |
| `useNotifications` | `() => NotificationState & Actions` | Hook for components | fetchNotifications |

---

## Test Plan

**Derived from Interface Contracts above.**

### fetchNotifications

**Signature:** `() => Promise<NotificationState>`

**Happy Path:**
- [ ] Returns notifications array with correct structure
- [ ] Calculates unreadCount correctly
- [ ] Sets isLoading states appropriately

**Edge Cases:**
- [ ] Empty array: returns `{ notifications: [], unreadCount: 0 }`
- [ ] Large dataset (100+ items): handles pagination if needed
- [ ] Malformed response: gracefully handles missing fields

**Error Cases:**
- [ ] Network failure: sets `error` state, `notifications` remains previous value
- [ ] 401 Unauthorized: triggers auth refresh or logout
- [ ] 500 Server Error: sets appropriate error message

**Mocks Needed:**
- `apiClient.get`: mock successful/failed responses

### markAsRead

**Signature:** `(id: string) => Promise<void>`

**Happy Path:**
- [ ] Updates notification read status to true
- [ ] Decrements unreadCount by 1
- [ ] Optimistically updates UI before server response

**Edge Cases:**
- [ ] Already read notification: no-op, no error
- [ ] Non-existent ID: handles gracefully
- [ ] Rapid successive calls: debounce or queue

**Error Cases:**
- [ ] Network failure: reverts optimistic update, shows toast
- [ ] Invalid ID (404): reverts update, logs error

**Mocks Needed:**
- `apiClient.patch`: mock success/failure

### markAllAsRead

**Signature:** `() => Promise<number>`

**Happy Path:**
- [ ] Marks all notifications as read
- [ ] Returns count of updated notifications
- [ ] Sets unreadCount to 0

**Edge Cases:**
- [ ] No unread notifications: returns 0, no API call needed
- [ ] Partial failure: reports how many succeeded

**Error Cases:**
- [ ] Network failure: reverts changes, shows error
- [ ] Timeout on large batch: handles gracefully

**Mocks Needed:**
- `apiClient.patch`: mock batch endpoint

### useNotifications (Hook)

**Signature:** `() => NotificationState & Actions`

**Happy Path:**
- [ ] Returns current state and action functions
- [ ] Initiates polling on mount
- [ ] Stops polling on unmount

**Edge Cases:**
- [ ] Tab becomes inactive: pauses polling
- [ ] Tab becomes active: resumes and fetches immediately
- [ ] Multiple components using hook: single source of truth

**Error Cases:**
- [ ] Context not provided: throws helpful error

**Mocks Needed:**
- `NotificationContext`: mock provider wrapper

---

## Files to Create/Modify

| File | Action | Purpose |
|------|--------|---------|
| `src/notifications/types.ts` | create | Type definitions |
| `src/notifications/service.ts` | create | API service functions |
| `src/notifications/NotificationContext.tsx` | create | Context + Provider |
| `src/notifications/useNotifications.ts` | create | Consumer hook |
| `src/components/NotificationBell.tsx` | create | UI component |
| `src/components/NotificationList.tsx` | create | Dropdown list |
| `src/components/Header.tsx` | modify | Add NotificationBell |

---

## Edge Cases to Handle

1. **Empty state** - No notifications ever received: show friendly empty message
2. **Error state** - API unavailable: show retry option
3. **Stale data** - Tab inactive then returns: immediate refresh
4. **Race condition** - Mark as read while fetch in progress: queue or dedupe
5. **Long messages** - Truncate with ellipsis, show full on hover

---

## Open Questions

- Should notifications persist across sessions (localStorage)?
- Maximum notification age before auto-archiving?
