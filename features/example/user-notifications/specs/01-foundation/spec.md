# User Notifications Foundation

**Status:** Example
**Created:** 2025-01-05
**Last Updated:** 2025-01-05
**Owner:** @your-team

---

## Overview

This spec covers the foundation of the notification system: the data types, service layer, and basic UI components. It establishes the polling mechanism and state management pattern that future specs will build upon.

---

## Out Of Scope

1. **Push Notifications** - Browser/mobile push requires service worker setup and is planned for a future spec.

2. **Notification Preferences** - User settings for notification types and frequency will be a separate feature.

3. **Email Notifications** - Email delivery is handled by a different system and feature.

4. **Notification Grouping** - Collapsing similar notifications (e.g., "5 new comments") is a future enhancement.

---

## Functional Requirements

### FR1: Notification Data Types

Define TypeScript interfaces for notifications.

**Requirements:**
- `Notification` interface with: id, type, message, timestamp, read status
- `NotificationType` enum: info, warning, action-required
- Export from `src/notifications/types.ts`

**Success Criteria:**
- [ ] Interfaces compile without errors
- [ ] Types exported and importable from other modules

---

### FR2: Notification Service

Create service for fetching and managing notifications.

**Requirements:**
- `fetchNotifications()` - GET from `/api/notifications`
- `markAsRead(id)` - PATCH to `/api/notifications/:id`
- `markAllAsRead()` - PATCH to `/api/notifications/read-all`
- Handle loading and error states

**Success Criteria:**
- [ ] Service methods call correct API endpoints
- [ ] Error handling covers network failures
- [ ] Loading state exposed for UI

---

### FR3: Notification Bell Component

Create header component showing notification count.

**Requirements:**
- Display bell icon (use existing icon library)
- Show unread count badge (hide if 0)
- Cap display at "99+"
- onClick opens NotificationList

**Success Criteria:**
- [ ] Badge shows correct unread count
- [ ] Badge hidden when count is 0
- [ ] Click triggers dropdown/modal

---

### FR4: Notification List Component

Create scrollable list of notifications.

**Requirements:**
- Show notification type icon, message, relative timestamp
- Visual distinction for unread vs read
- Click notification to mark as read
- "Mark all as read" button in header

**Success Criteria:**
- [ ] Notifications render with correct styling
- [ ] Unread notifications visually distinct
- [ ] Mark as read updates UI immediately

---

## Technical Design

### Files to Reference
- `src/components/Header.tsx` - Where bell will be placed
- `src/api/client.ts` - Existing API client pattern
- `src/hooks/usePolling.ts` - If exists, reuse for refresh

### Files to Create/Modify
| File | Action | Purpose |
|------|--------|---------|
| `src/notifications/types.ts` | Create | Type definitions |
| `src/notifications/service.ts` | Create | API service layer |
| `src/components/NotificationBell.tsx` | Create | Header bell component |
| `src/components/NotificationList.tsx` | Create | Dropdown list |
| `src/components/Header.tsx` | Modify | Add NotificationBell |

### API Contract

```typescript
// GET /api/notifications
Response: {
  notifications: Notification[];
  unreadCount: number;
}

// PATCH /api/notifications/:id
Body: { read: true }
Response: { success: boolean }

// PATCH /api/notifications/read-all
Response: { success: boolean; updatedCount: number }
```

---

## Dependencies

### Internal
- Existing API client (`src/api/client.ts`)
- Icon library already in use
- Header component for integration point

### External
- None for this spec

### Assumptions
- Backend API endpoints exist or will be created in parallel
- Authentication/session handling already in place
