# Borchlympics — Conversation Archive

## Overview
This document archives the full conversation that produced the Borchlympics tournament tracker, so any collaborator has full context on decisions made and problems solved.

---

## Session Summary

### 1. Initial Request
**User asked:** Create a video game tournament called the "Borchlympics" spanning multiple games to crown an ultimate champion.

**Games specified:**
- Fall Guys
- Golf With Friends
- NCAA Football 26
- FIFA / EA Sports FC
- NBA 2K26
- Battlefield
- EA Sports UFC
- Madden 26 (added shortly after)

Claude also scraped the Xbox Game Pass library and suggested additional games. Madden 26 was accepted.

---

### 2. Tournament Structure Decisions
**Players:** 3 (expandable to 8)
**Vibe:** Mix of casual and competitive
**Champion decided by:** Points accumulation across all games

**Points system:**
- 🥇 1st place = 10 pts
- 🥈 2nd place = 6 pts
- 🥉 3rd place = 3 pts
- 🎖️ 4th+ = 1 pt (participation)

**Tiebreakers:**
1. Most 1st place finishes
2. Most 2nd place finishes
3. Fall Guys sudden death

---

### 3. First Build — localStorage Version
Built a full interactive HTML tournament tracker with:
- Standings podium + leaderboard
- Event schedule with status indicators
- Score entry dropdowns per player per event
- Rules page
- Player management
- Data saved to `localStorage` (device-local only)

---

### 4. Firebase Upgrade Request
User asked for live sync so all players see the same data across devices.

**Decision:** Firebase Realtime Database (free Spark plan)

**First attempt:** Used Firebase JS SDK (compat version 9.23.0)
- Caused connection errors on Netlify
- Error: "Could not reach database"

**Debugging steps:**
- Confirmed Firebase URL format was correct
- Confirmed rules were set to `false` (root cause found)
- User updated rules to `true` and published
- Still failed — confirmed to be SDK initialization issue, not rules

**Confirmed database was open** by having user visit:
`https://borchlympics-default-rtdb.firebaseio.com/.json`
→ Returned `null` (correct — database open, just empty)

---

### 5. Final Fix — REST API Approach
**Dropped the Firebase SDK entirely.**

Replaced with direct `fetch()` calls to Firebase REST endpoints:
```
GET    https://project.firebaseio.com/path.json       → read
PUT    https://project.firebaseio.com/path.json       → set/overwrite
PATCH  https://project.firebaseio.com/path.json       → partial update
DELETE https://project.firebaseio.com/path.json       → delete
```

This bypassed all SDK initialization issues and worked immediately.

**Sync method:** Polling every 3 seconds via `setInterval(fetchAll, 3000)`

---

### 6. Editable Games Feature
Added full event management:
- ✏️ Edit event name and format inline
- Emoji picker (click the emoji icon)
- ▲▼ reorder events
- ✕ remove events
- ＋ Add Custom Game button with emoji picker, name, and format fields
- All changes write to Firebase and sync to all players

---

### 7. Deployment
- File named `index.html` (ready for Netlify Drop)
- User drags file to netlify.com/drop
- Gets shareable URL
- All players open same URL, enter Firebase DB URL on first load
- URL saved to localStorage so only entered once per device

---

## Key Files
| File | Description |
|------|-------------|
| `index.html` | The complete app — everything in one file |
| `README.md` | Project overview and technical reference |
| `CONVERSATION.md` | This file — full session archive |

---

## Firebase Details
- **Project name:** borchlympics
- **Database URL:** `https://borchlympics-default-rtdb.firebaseio.com`
- **Rules:** `.read: true, .write: true` (test mode)
- **Plan:** Spark (free, no credit card)

---

## Known Issues / Notes
- Polling at 3s intervals means updates aren't instantaneous — there's up to a 3 second delay between one player entering a score and another seeing it update. For a casual tournament this is fine. Could be improved with SSE (Server-Sent Events) from Firebase streaming endpoint.
- Firebase test mode rules expire after 30 days in some configurations — user should check the Firebase console if the app stops working in the future.
- The setup screen asks for the DB URL every time on a new device/browser — this is by design (localStorage saves it after first entry).
