# World Cup 2026 Comp

A single-file web app for running a World Cup sweepstake competition with friends. Players pick one team from each of 8 brackets and earn points as their teams progress through the tournament.

## Quick Start

1. Set up Firebase (free) — see Step 1 below
2. Paste your Firebase config into `index.html`
3. Upload `index.html` to GitHub Pages
4. Share the URL with players

---

## Step 1: Firebase Setup

### Create a Firebase Project
1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → name it e.g. `worldcup-comp-2026`
3. Disable Google Analytics (not needed) → click **Create**

### Enable Realtime Database
1. Go to **Build → Realtime Database**
2. Click **Create Database**
3. Choose a region close to you (e.g. `europe-west1` for Ireland/UK)
4. Start in **Test Mode** → click **Enable**

### Get Your Web App Config
1. Go to **Project Settings** (gear icon, top left)
2. Under "Your apps" click the **Web** icon (`</>`)
3. Register the app (any name) → copy the config block

### Paste Config into index.html
Open `index.html` and find this section near the top of the `<script>` tag:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT.firebaseapp.com",
    databaseURL: "https://YOUR_PROJECT-default-rtdb.YOUR_REGION.firebasedatabase.app",
    projectId: "YOUR_PROJECT"
};
```

Replace the placeholder values with your actual Firebase config values.

### (Optional) Lock Down the Database
After the competition, update Firebase database rules to read-only:
```json
{
  "rules": {
    ".read": true,
    ".write": false
  }
}
```
For a small friend group, Test Mode (open read/write) is fine.

---

## Step 2: Host on GitHub Pages

1. Create a new repository on GitHub (e.g. `worldcup-comp`)
2. Upload `index.html` to the repository
3. Go to **Settings → Pages**
4. Under "Source" select **Deploy from a branch** → choose `main` → `/ (root)` → Save
5. After a minute, your site will be live at `https://yourusername.github.io/worldcup-comp`
6. Share that URL with your players

---

## Step 3: Admin First Steps

### Login as Admin
- Username: `admin`
- Password: `admin123`
- **Change this immediately** in Admin → Settings → New Admin Password

### Set Up Brackets
1. Go to **Admin → Brackets**
2. For each of the 8 brackets, enter a name (e.g. "Bracket 1") and 4 team names (one per line)
3. Use the odds-based bracket method: rank all 32 teams by tournament winner odds, then assign one team from each "tier" to each bracket — this gives every bracket a similar spread of strong and weak teams
4. Click **Save All Brackets**

### Set the Picks Deadline
1. Go to **Admin → Settings**
2. Set the **Picks Deadline** to just before the first match kicks off
3. After the deadline, players can no longer change their picks

### Share the URL
Send the GitHub Pages link to your players. They register with a username and password, then pick one team from each bracket.

---

## Step 4: Scoring Setup

### Option A: Manual (Recommended to start)
After each match, go to **Admin → Match Results** and enter:
- Home team name
- Away team name
- Round (Group Stage / Last 16 / Quarter Final / Semi Final / Final)
- Result (Home Win / Draw / Away Win)

The leaderboard updates automatically for all players.

### Option B: ESPN Live Scores (Auto-fetch, Free, No Key Needed)
ESPN exposes the same public JSON API used in the Masters golf comp — no account, no API key, completely free.

1. In the app: **Admin → ESPN Live Scores**
2. Set Scoring Mode to **ESPN API (Auto + Manual Override)**
3. The League Slug is pre-set to `fifa.world` (correct for the FIFA World Cup) — leave it as-is
4. Set refresh interval (90 seconds is recommended)
5. Click **Test ESPN Fetch** to verify it connects
6. Click **Save Config**

When enabled, the admin's browser polls ESPN every 90 seconds and writes completed match results to Firebase. All players see updates automatically via the real-time database.

**Important:** Only the admin's browser drives the polling. Manual match results always take priority over ESPN-fetched results. ESPN is an undocumented but stable API — it has been reliable for years across multiple sports.

---

## Points Scoring

| Round | Win | Draw |
|-------|-----|------|
| Group Stage | 2 pts | 1 pt |
| Last 16 | 5 pts | — |
| Quarter Final | 8 pts | — |
| Semi Final | 10 pts | — |
| Final | 20 pts | — |

(Knockout rounds have no draws — the winner goes through.)

### Example
A player who picked Brazil, France, Germany, Spain, Argentina, Morocco, Japan, and USA could earn:
- Brazil wins 3 group games: +6
- France wins 2 group games, draws 1: +5
- Brazil wins Last 16: +5
- Brazil wins QF: +8
- ...and so on

---

## How It Works

### For Players
1. Go to the site URL → Register with a username and password
2. Go to **My Picks** → select 1 team from each of the 8 brackets
3. Click **Save My Picks** before the deadline
4. Watch the **Leaderboard** update as matches are played

### For Admin
- Add brackets and teams before the competition starts
- Set the picks deadline
- Enter match results (manually or via API-Football)
- The leaderboard updates automatically for everyone

---

## Bracket Strategy (Odds-Based)

The fairest way to build brackets is to rank all 32 teams by their tournament winner odds (e.g. from Betfair or Paddy Power), then assign them to brackets in a snake order:

```
Rank 1  → Bracket 1
Rank 2  → Bracket 2
...
Rank 8  → Bracket 8
Rank 9  → Bracket 8   (snake back)
Rank 10 → Bracket 7
...
Rank 16 → Bracket 1
Rank 17 → Bracket 1   (snake again)
...
```

This ensures each bracket contains one top-tier, one mid-tier, and two lower-tier teams — no bracket is significantly stronger than another.

---

## Troubleshooting

**"Failed to connect to Firebase"** — Check your `firebaseConfig` values in `index.html`. Make sure the `databaseURL` field is included (it's sometimes missing from the default config snippet — copy it from the Realtime Database section in the Firebase console).

**Players can't register** — Make sure the Firebase database is in Test Mode (or has write access enabled in the rules).

**ESPN fetch returns no results** — This is normal before the tournament starts. Completed matches will appear once games are played. Use Test ESPN Fetch to confirm the connection works.

**Picks not saving** — Check the browser console for errors. Usually a Firebase permissions issue.
