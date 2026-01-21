# Feature: User Notifications

## Feature ID

`user-notifications`

## Metadata

| Field | Value |
|-------|-------|
| **Domain** | Example |
| **Feature Name** | User Notifications |
| **Contributors** | @your-team |

## Files Touched

### Backend
- `src/notifications/service.ts` (new)
- `src/notifications/types.ts` (new)

### Frontend
- `src/components/NotificationBell.tsx` (new)
- `src/components/NotificationList.tsx` (new)

### Tests
- `src/notifications/__tests__/service.test.ts` (new)

## Table of Contents

- [Feature Overview](#feature-overview)
- [Intended State](#intended-state)
- [System Architecture](#system-architecture)
- [User Experience](#user-experience)
- [Changelog of Feature Specs](#changelog-of-feature-specs)

## Feature Overview

### Summary

A notification system that allows users to receive and view in-app notifications. Notifications appear in a bell icon in the header and can be marked as read.

### Problem Statement

Users currently have no way to receive updates about important events (mentions, task assignments, deadlines). They must manually check for changes.

### Goals

- Display unread notification count in header
- Show notification list when bell is clicked
- Mark notifications as read (individually or all)
- Support different notification types (info, warning, action-required)

### Non-Goals

- Push notifications (browser/mobile) - future enhancement
- Email notifications - separate feature
- Notification preferences/settings - separate feature

## Intended State

When complete, users will see a notification bell in the application header. The bell displays an unread count badge. Clicking it opens a dropdown with recent notifications. Each notification shows its type, message, timestamp, and can be dismissed.

### Key Behaviors

1. **Unread Badge**: Shows count of unread notifications (max "99+")
2. **Notification List**: Scrollable list of recent notifications
3. **Mark as Read**: Click to dismiss; "Mark all read" button
4. **Auto-refresh**: Poll for new notifications every 30 seconds

## System Architecture

### Component Structure

```
src/
├── notifications/
│   ├── service.ts       # API calls, polling logic
│   ├── types.ts         # Notification interfaces
│   └── __tests__/
└── components/
    ├── NotificationBell.tsx
    └── NotificationList.tsx
```

### Data Flow

```
Backend API
    ↓
NotificationService (polling)
    ↓
NotificationContext (state)
    ↓
NotificationBell → NotificationList
```

## User Experience

### User Story 1: View Notifications

**As a** user
**I want** to see my notifications
**So that** I stay informed about relevant events

**Success Criteria:**
- [ ] Bell icon visible in header
- [ ] Unread count badge displays correctly
- [ ] Clicking bell opens notification list

### User Story 2: Manage Notifications

**As a** user
**I want** to mark notifications as read
**So that** I can track what I've seen

**Success Criteria:**
- [ ] Can mark individual notifications as read
- [ ] Can mark all notifications as read
- [ ] Read status persists across sessions

## Changelog of Feature Specs

| Date | Spec | Description |
|------|------|-------------|
| TBD | [01-foundation](specs/01-foundation/spec.md) | Core notification service and UI components |
