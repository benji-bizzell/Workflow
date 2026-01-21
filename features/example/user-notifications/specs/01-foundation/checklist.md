# Implementation Checklist

Spec: `01-foundation`
Feature: `user-notifications`

---

## Phase 1.0: Test Foundation

### FR1: Notification Data Types
- [ ] Write type validation tests
- [ ] Test type exports are accessible

### FR2: Notification Service
- [ ] Write tests for `fetchNotifications`
- [ ] Write tests for `markAsRead`
- [ ] Write tests for `markAllAsRead`
- [ ] Test error handling scenarios

### FR3: Notification Bell Component
- [ ] Test renders bell icon
- [ ] Test badge shows correct count
- [ ] Test badge hidden when count is 0
- [ ] Test "99+" cap behavior
- [ ] Test click handler fires

### FR4: Notification List Component
- [ ] Test renders notification items
- [ ] Test unread styling applied
- [ ] Test mark as read on click
- [ ] Test mark all as read button
- [ ] Test empty state rendering

---

## Phase 1.1: Implementation

### FR1: Notification Data Types
- [ ] Create `src/notifications/types.ts`
- [ ] Define `NotificationType` enum
- [ ] Define `Notification` interface
- [ ] Define `NotificationState` interface
- [ ] Export all types

### FR2: Notification Service
- [ ] Create `src/notifications/service.ts`
- [ ] Implement `fetchNotifications`
- [ ] Implement `markAsRead`
- [ ] Implement `markAllAsRead`
- [ ] Add error handling
- [ ] Create `useNotifications` hook

### FR3: Notification Bell Component
- [ ] Create `src/components/NotificationBell.tsx`
- [ ] Add bell icon from icon library
- [ ] Implement badge with count
- [ ] Add click handler for dropdown
- [ ] Integrate into Header.tsx

### FR4: Notification List Component
- [ ] Create `src/components/NotificationList.tsx`
- [ ] Implement notification item rendering
- [ ] Add unread/read styling
- [ ] Implement mark as read
- [ ] Add "Mark all as read" button
- [ ] Handle empty state

---

## Verification

- [ ] All tests passing
- [ ] No TypeScript errors
- [ ] Manual testing complete
- [ ] Code review approved
- [ ] FEATURE.md changelog updated

---

## Session Notes

<!-- Add notes as you work -->

**Example format:**
```
**2025-01-05**: Started Phase 1.0. Completed FR1 and FR2 tests.
FR3 tests blocked - need to confirm icon library import path.
```
