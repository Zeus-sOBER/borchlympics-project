# 🏆 Borchlympics — Season Edition

A multiplayer video game **season tracker** — async-friendly, timezone-proof, Firebase-powered. Anyone can log a match. Points accumulate over a season. Champions get immortalized in the Hall of Fame.

---

## ✨ Features

- **Season system** — Admin manually starts and ends seasons. Each season has a name, match log, and leaderboard.
- **Async match logging** — Anyone can log a match between any players at any time. No need to all be online simultaneously.
- **Game point multipliers** — Each game gets a tier (1× Standard → 5× Grand Slam), so bigger games are worth more.
- **Live sync** — Firebase Realtime Database keeps everyone's view in sync via polling.
- **Hall of Fame** — Every season champion is permanently recorded.
- **Single HTML file** — Zero dependencies. Zero build step. Just one file.

---

## 🚀 Quick Deploy (GitHub Pages)

1. **Fork or push this repo to GitHub**
2. Go to your repo → **Settings → Pages**
3. Source: **Deploy from a branch** → `main` → `/ (root)` → Save
4. Your app will be live at: `https://<your-username>.github.io/<repo-name>/`

That's it. No build process.

---

## 🔥 Firebase Setup (free, 2 min)

1. Go to [console.firebase.google.com](https://console.firebase.google.com) → **Add project**
2. Left sidebar → **Build → Realtime Database → Create Database**
3. Choose location → **Start in test mode**
4. Go to **Rules tab** → set both `.read` and `.write` to `true` → **Publish**
5. Copy the Database URL (e.g. `https://your-project-default-rtdb.firebaseio.com`)
6. Paste it into the app when prompted

The URL is saved in `localStorage` so you only need to enter it once per browser.

---

## 🗄️ Firebase Database Structure

```
borchlympics/
├── activeSeason/          { id, name, startDate, active }
├── players/
│   └── {playerId}:        { id, name, emoji }
├── games/
│   └── {gameId}:          { id, name, emoji, format, multiplier, order }
├── matches/
│   └── {matchId}:         { seasonId, gameId, gameName, gameEmoji, date, results: { playerId: position } }
└── hof/                   (Hall of Fame — written when a season ends)
    └── {seasonId}:        { id, name, startDate, endDate, winnerId, winnerName, winnerEmoji, winnerPts, matchCount }
```

---

## 🎯 Points System

| Finish | Base Points |
|--------|------------|
| 🥇 1st | 10 pts     |
| 🥈 2nd | 6 pts      |
| 🥉 3rd | 3 pts      |
| 4th+   | 1 pt       |

**Game multipliers** are applied on top:

| Tier        | Multiplier | Example              |
|-------------|-----------|----------------------|
| Standard    | 1×        | Fall Guys, Battlefield |
| Featured    | 2×        | UFC, FIFA, NBA 2K    |
| Major       | 3×        | NCAA 26, Madden 26   |
| Grand Slam  | 5×        | Special events       |

A 1st place finish in a 3× Major game = **30 points**.

---

## 🎮 Default Games

| Game | Multiplier | Format |
|------|-----------|--------|
| 🪂 Fall Guys | 1× | 1 Episode — First to win |
| ⛳ Golf With Friends | 1× | Full course — Lowest score wins |
| 🥊 EA Sports UFC | 2× | Round robin — 3-round fights |
| 🏈 NCAA Football 26 | 3× | 1 game — 4 min quarters |
| ⚽ EA Sports FC / FIFA | 2× | 1 game — 6 min halves |
| 💣 Battlefield | 1× | Team Deathmatch — 10 min |
| 🏀 NBA 2K26 | 2× | 1 game — 6 min quarters |
| 🏟️ Madden NFL 26 | 3× | 1 game — 4 min quarters |

All fully editable in the app.

---

## 🏗️ Tech Stack

- **Frontend:** Vanilla HTML/CSS/JS — zero dependencies, zero build step
- **Database:** Firebase Realtime Database (REST API via `fetch` — no SDK needed)
- **Hosting:** GitHub Pages (or Netlify Drop)
- **Fonts:** Google Fonts (Bebas Neue + Inter)

---

## 📋 How to Run a Season

1. **Admin → Start Season** — Give it a name (e.g. "May 2026 Season")
2. Players log matches via **Matches → Log a Match** as they play throughout the month
3. Points accumulate on the **Standings** leaderboard in real time
4. **Admin → End Season & Crown Champion** — Saves the winner to the Hall of Fame and resets for the next season

---

## 🗺️ Roadmap

- [ ] Player emoji picker
- [ ] Per-match notes / trash talk log
- [ ] WebSocket / SSE for true real-time (instead of polling)
- [ ] Season stats breakdown (most wins per game, etc.)
- [ ] Champion celebration screen
- [ ] Export season to image/CSV
