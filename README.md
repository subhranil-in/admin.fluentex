# 🛡️ Fluentex Admin Panel

> **Version:** v1.8.0 &nbsp;|&nbsp; **Built by:** SubhraniL &nbsp;|&nbsp; **© 2026 Classify Technologies LLC**

---

## Table of Contents

1. [Overview](#overview)
2. [Accessing the Admin Panel](#accessing-the-admin-panel)
3. [Default Credentials](#default-credentials)
4. [Pages & Features](#pages--features)
   - [Dashboard](#dashboard)
   - [Users](#users)
   - [Analytics](#analytics)
   - [Live Feed](#live-feed)
   - [Feedback](#feedback)
   - [Chat Moderation](#chat-moderation)
   - [Settings](#settings)
5. [Push Notifications](#push-notifications)
6. [Data Sources](#data-sources)
7. [Storage Keys Reference](#storage-keys-reference)
8. [BroadcastChannels Reference](#broadcastchannels-reference)
9. [Exporting Data](#exporting-data)
10. [Security Notes](#security-notes)

---

## Overview

The Fluentex Admin Panel is a standalone single-HTML-file dashboard that gives administrators full visibility and control over the Fluentex learning platform. It reads directly from `localStorage` (shared with the Fluentex app when both are open in the same browser) and communicates with the live app in real time via the Web `BroadcastChannel` API.

No server, no database, and no build step required. Open the file in the same browser as Fluentex and all data is instantly available.

---

## Accessing the Admin Panel

1. Open `fluentex-admin.html` in any modern browser.
2. You are presented with the login screen.
3. Enter the admin username and password.
4. Upon successful login, you land on the **Dashboard**.

> **Important:** The Admin Panel must be opened in the **same browser** as the Fluentex app (same browser profile) to share `localStorage` data and receive real-time BroadcastChannel events. It does not need to be in the same tab.

---

## Default Credentials

| Field | Default Value |
|-------|--------------|
| Username | `admin` |
| Password | `fluentex2026` |

Credentials are stored in `localStorage` under `fx_admin_user` and `fx_admin_pass`. They can be changed at any time from the **Settings** page. The admin panel reads the stored credentials on every login attempt, so changes take effect immediately.

> ⚠️ **Change the default password** before deploying in any shared or production environment.

---

## Pages & Features

### Dashboard

The landing page after login. Shows a high-level overview of the entire platform at a glance.

**Stat Cards (top row)**

| Card | Metric |
|------|--------|
| 👥 Total Users | Number of distinct users in the leaderboard |
| ⚡ Total XP Earned | Sum of XP across all users |
| 📚 Total Quizzes | Sum of quiz completions across all users |
| 🔥 Active Streaks | Number of users with a streak > 0 |
| 🏅 Badges Awarded | Total badge count across all users |
| 🎯 Avg. Accuracy | Platform-wide average accuracy across all categories |

**Leaderboard Snapshot**

A sortable table showing all users ranked by XP. Columns:

`#` · `User` · `XP ▼` · `Streak` · `Quizzes` · `Badges` · `Top Category`

Clicking **Refresh** at the top re-reads localStorage and recalculates all stats.

---

### Users

A searchable, paginated table of all registered learners.

**Columns:** `#` · `User` · `Flag` · `XP` · `Streak` · `Quizzes` · `Badges` · `Accuracy` · `Top Category` · `Joined`

**Features:**
- Search by name in real time
- Paginate through all users (10 per page)
- The total user count badge on the sidebar auto-updates on load
- Click any row to expand full profile details (planned)

---

### Analytics

Aggregated learning analytics across the entire platform.

**Accuracy by Category** — Bar chart showing average accuracy per topic (Vocabulary, Grammar, Idioms, Spelling, Conversation, Reading). Highlights the platform's strongest and weakest categories.

**XP Distribution** — Breakdown of learners by XP tier (0–99, 100–499, 500–999, 1,000–4,999, 5,000+).

**Streak Distribution** — Breakdown of active streaks by length (1–6 days, 1–2 weeks, 2–4 weeks, 1–3 months, 3+ months).

**Top Performers** — The top 5 users by XP with their full stats.

All analytics are computed client-side from localStorage data on page load or manual refresh.

---

### Live Feed

A real-time event stream showing the most recent activity across the platform. The feed auto-updates via `BroadcastChannel` and a polling interval.

**Event types shown:**
- 📬 New feedback submission (suggestion or bug report)
- 👤 User profile updates
- ⚡ Quiz completions
- 💬 Leaderboard updates

The feed keeps the last 100 events in memory (cleared on page refresh). A pulsing green dot on the sidebar indicates the feed is live.

---

### Feedback

A full inbox for user-submitted feature suggestions and bug reports sent from the Fluentex app.

**Stat Cards:**

| Card | Metric |
|------|--------|
| 💡 Suggestions | Total feature suggestions received |
| 🐛 Bug Reports | Total bug reports received |
| 📬 Unreviewed | Submissions not yet marked as reviewed |
| ✅ Reviewed | Submissions marked as reviewed |

**Tabs:** 💡 Suggestions &nbsp;|&nbsp; 🐛 Bug Reports

**Filters:**
- Search by name or message content
- Filter by status (All / Unreviewed / Reviewed)
- Filter by category

**Per-submission actions:**
- **🔔 Notify User** — opens the push notification modal pre-filled with a reply to that submission
- **✅ Mark Reviewed** — marks the submission as reviewed (visible to admin only)
- **🗑 Delete** — removes the submission permanently from localStorage

**📣 Broadcast to All Users** — opens the notification modal targeting all users at once.

New submissions from the live Fluentex app arrive in real time via `BroadcastChannel('fluentex_lb')` and appear in the feed without a page refresh.

---

### Chat Moderation

Full moderation control over the Community Chat in the Fluentex app.

**Stat Cards:**

| Card | Metric |
|------|--------|
| 💬 Total Messages | All messages ever sent (including removed) |
| 👥 Unique Users | Distinct authors who have posted |
| 🚫 Removed Msgs | Messages marked as deleted |
| ⚠️ Phone Flags | Messages detected to contain a phone number |

**Filters:**
- Search by message text or username
- Filter by type: All Messages / Active Only / Removed Only / Mod Messages
- Filter by specific user (dropdown auto-populated from all message authors)

**Per-message actions:**
- **🗑 Remove** — marks the message as deleted. The deletion is broadcast to all open Fluentex tabs via `BroadcastChannel('fluentex_chat')` so it disappears from the live chat immediately.
- **↩ Restore** — re-activates a previously removed message.
- **🔔 Warn User** — opens the push notification modal pre-filled with a community guidelines warning targeting that user.

**🗑 Clear All** — deletes the entire chat history after confirmation. Broadcast to all open Fluentex tabs immediately.

**Load More** — messages are shown 40 at a time (newest first). Click to load older batches.

The sidebar badge on **Chat Mod** shows the count of messages active in the last hour as a quick activity indicator.

---

### Settings

Admin account and system configuration.

**Change Credentials**
- Change admin username
- Change admin password
- Changes take effect on the next login attempt
- Stored in `fx_admin_user` / `fx_admin_pass` in localStorage

**System Status**
- `checkSystemStatus()` validates that localStorage is available and readable
- Displays the current Fluentex data version keys detected in storage

**Danger Zone**
- **Reset All User Data** — clears all Fluentex localStorage keys (profile, leaderboard, chat, feedback, bots, notifications). Requires typing a confirmation phrase.

---

## Push Notifications

The admin can send push notifications to individual users or all users from the **Feedback** and **Chat Moderation** pages, or directly via the broadcast button.

**Notification Modal fields:**

| Field | Options |
|-------|---------|
| Target | `_broadcast` (all users) or a specific username |
| Type | Info · Success · Warning · Announcement · Reply |
| Title | Free text (shown as notification heading) |
| Message | Free text (notification body) |

**Delivery mechanism:**
1. Notification object written to `fx_notifs_broadcast` (for broadcasts) or `fx_notifs_u_{slug}` (for targeted)
2. `BroadcastChannel('fluentex_notif')` fires immediately — users with the Fluentex app open in the same browser receive it instantly
3. Users who open Fluentex later pick it up via 30-second localStorage polling

---

## Data Sources

All data is read from the same `localStorage` instance as the Fluentex app. The admin panel does **not** write to user profile data (`fx_pro_v5`) — it only reads it. The only keys the admin panel writes to are:

- `fx_chat_v2` — when removing or restoring messages
- `fx_notifs_broadcast` — when sending a broadcast notification
- `fx_notifs_u_{slug}` — when sending a targeted notification
- `fx_admin_user` / `fx_admin_pass` — admin credentials
- `fx_admin_settings` — admin preferences

---

## Storage Keys Reference

| Key | Read / Write | Contents |
|-----|-------------|----------|
| `fx_pro_v5` | Read | Active user's profile |
| `fx_lb_v6` | Read | Leaderboard (all users + bots) |
| `fx_bots_v2` | Read | Bot learner states |
| `fx_chat_v2` | Read + Write | Community chat messages |
| `fx_suggests_local` | Read + Write | Feature suggestions |
| `fx_bugs_local` | Read + Write | Bug reports |
| `fx_notifs_broadcast` | Write | Broadcast push notifications |
| `fx_notifs_u_{slug}` | Write | Targeted push notifications |
| `fx_admin_user` | Read + Write | Admin username |
| `fx_admin_pass` | Read + Write | Admin password |
| `fx_admin_settings` | Read + Write | Admin panel preferences |

---

## BroadcastChannels Reference

| Channel | Direction | Payload | Purpose |
|---------|-----------|---------|---------|
| `fluentex_lb` | App → Admin | `{type:'new_feedback', item}` | New suggestion or bug report arrives live |
| `fluentex_notif` | Admin → App | `{type:'push_notif', notif}` | Push notification delivered to open Fluentex tabs |
| `fluentex_chat` | Admin → App | `{type:'chat_update', ts}` | Chat updated (message removed/restored), triggers re-render in app |

---

## Exporting Data

The **⬇ Export CSV** button (top-right of every page) exports the full leaderboard as a `.csv` file with the following columns:

```
Name, Flag, XP, WeekXP, Streak, Quizzes, Badges, Vocabulary Acc, Grammar Acc,
Idioms Acc, Spelling Acc, Conversation Acc, Reading Acc, Top Category
```

The export uses the current in-memory data (same as what's displayed on the Dashboard). Click **Refresh** first to ensure the latest data is loaded.

---

## Security Notes

- **Same-origin only** — The admin panel only works when opened in the same browser as the Fluentex app. There is no remote access or API authentication.
- **localStorage is not encrypted** — All user data, chat messages, and admin credentials are stored in plaintext in the browser's localStorage. Do not store sensitive personal information.
- **Change the default password** — The default credentials (`admin` / `fluentex2026`) are publicly documented. Always update them before any shared deployment.
- **No session expiry** — The admin session persists in `sessionStorage` until the tab is closed. There is no automatic timeout.
- **BroadcastChannel is same-origin** — Real-time communication via BroadcastChannel only works between pages served from the same origin (or opened as local files in the same browser session).

---

*Made with ❤️ in India · Developed by SubhraniL · © 2026 Classify Technologies LLC*
