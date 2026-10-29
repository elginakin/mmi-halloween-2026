# A Lab Murder: setup, deploy and run-of-show

Technical guide for whoever sets up and runs the app. For what the project is, see
[README.md](README.md). For the MC script and the cast briefing, see [MC_SCRIPT.md](MC_SCRIPT.md)
and [SUSPECTS.md](SUSPECTS.md).

> ⚠️ This file contains the **answer** (see "Host solution"). Don't show it to guests.

## The three pages

Firebase project: **`mmi-halloween-2026`** (already set in `.firebaserc`).

| Page | URL | Who opens it |
|---|---|---|
| **Vote** | `https://mmi-halloween-2026.web.app/` | Audience phones (the QR code points here) |
| **Stage** | `https://mmi-halloween-2026.web.app/stage-nkddi5.html` | The laptop driving the projector |
| **Control** | `https://mmi-halloween-2026.web.app/control-k7427s.html` | The host's phone (or laptop) |
| **Crew** | `https://mmi-halloween-2026.web.app/crew-okzzjf.html` | Host, MC and suspects: links to everything + live MC script |

The stage and control paths are not linked from anywhere. Don't share them. There
are no passwords, so anyone who has the control URL can run the show (and see the
answer in its source code).

## Files

```
firebase.json              Hosting + Firestore rules config
firestore.rules            Explicit open rules (they do not expire like "test mode" does)
public/index.html          Vote page (phones)
public/stage-nkddi5.html   Projector screen
public/control-k7427s.html Host controls (the ONLY file that contains the answer)
public/crew-okzzjf.html    Crew homepage: links, mystery, cast briefing, live MC script
public/firebase-config.js  Firebase web config (git-ignored, deployed)
firebase-config.example.js Template for the above (committed, not deployed)
README.md                  What the project is + the story
DEPLOY.md                  This file
MC_SCRIPT.md               Script for the MC (about 2 minutes)
SUSPECTS.md                Briefing to send to the 7 cast members
```

Each page is fully self-contained: no shared JS. If you change suspect names, weapons,
rooms or emoji, change them in all three files.

---

## Set up and deploy (about 20 minutes)

### 1. Install the tools (once)

1. Install **Node.js LTS** from <https://nodejs.org> (the big green "LTS" button). Run the installer.
2. Open a terminal (macOS: Terminal app) and run:
   ```bash
   npm install -g firebase-tools
   firebase --version
   ```
   You should see a version number such as `13.x` or `14.x`.

### 2. Create the Firebase project

1. Go to <https://console.firebase.google.com> and sign in with your Google account.
2. Click **Create a project** (or **Add project**).
3. Name it `mmi-halloween-2026` and make sure the **project ID** shown under the name is also `mmi-halloween-2026` (it becomes part of your URL; if it differs, update `.firebaserc` and the URLs in this README). Click **Continue**.
   *(Skip this step if the project already exists.)*
4. If asked about Gemini / AI assistance, either choice is fine. Click **Continue**.
5. **Google Analytics:** switch it **off**. Click **Create project**, wait, then click **Continue**.

### 3. Create the Firestore database

1. In the left sidebar, open **Build → Firestore Database** (in newer consoles it may be under **Databases & Storage**).
2. Click **Create database**.
3. If asked for an edition, choose **Standard**. Leave the Database ID as `(default)`.
4. **Location:** choose the one closest to the party (e.g. `nam5 (United States)`). This cannot be changed later. Click **Next**.
5. Choose **Start in production mode** (NOT test mode; test mode stops working after 30 days). Click **Create**.
   Production mode locks everything for now. Our own open rules are deployed in step 6.

### 4. Register a web app and add the config

The config lives in **`public/firebase-config.js`**. That file is in `.gitignore`, so it is
never committed to GitHub, but `firebase deploy` still uploads it (browsers need it to
reach Firestore). All three pages load it. If it's missing, they fall back to the config
Firebase Hosting serves at `/__/firebase/init.json`.

> For `mmi-halloween-2026` this file already exists on the original machine. Do the steps
> below only on a fresh clone, or if you create a new project.

1. Click the **gear icon ⚙ → Project settings** (top of the left sidebar).
2. On the **General** tab, scroll to **Your apps**. If there is no web app yet, click the
   **Web** icon (`</>`), name it (e.g. `lab-murder-web`), leave "Also set up Firebase Hosting"
   unchecked and click **Register app**.
3. Under **SDK setup and configuration**, choose **Config** and copy the values.
4. Copy the template and fill it in:
   ```bash
   cp firebase-config.example.js public/firebase-config.js
   ```
   Then open `public/firebase-config.js` and paste in `apiKey`, `messagingSenderId` and `appId`.
5. Check that git ignores it: `git status` must **not** list `public/firebase-config.js`.

**About secrecy:** the Firebase web config is not a password. Every visitor's browser
downloads it, so anyone at the party can see it with dev tools. Keeping it out of GitHub
stops bots that scrape repos for keys. What actually protects the data is
`firestore.rules`, which only opens the four collections this app uses. Optional extra
hardening: in Google Cloud Console → **APIs & Services → Credentials**, restrict the
"Browser key" to the HTTP referrers `mmi-halloween-2026.web.app/*` and
`mmi-halloween-2026.firebaseapp.com/*`.

### 5. Log the CLI in

This folder is already linked to `mmi-halloween-2026` through `.firebaserc`. In a terminal,
from this project folder (the one containing `firebase.json`):

```bash
firebase login
firebase projects:list
```

`firebase login` opens a browser; approve it. `firebase projects:list` should include
`mmi-halloween-2026`. If it doesn't, you're logged in with a different Google account
from the one that owns the project.

### 6. Deploy

```bash
firebase deploy
```

This uploads the three pages to Hosting **and** publishes `firestore.rules`. At the end
it prints:

```
Hosting URL: https://mmi-halloween-2026.web.app
https://mmi-halloween-2026.web.app/stage-nkddi5.html
https://mmi-halloween-2026.web.app/control-k7427s.html
```

That's your vote URL. Add `/stage-nkddi5.html` and `/control-k7427s.html` for the
other two pages.

Check the rules: in the console, open **Firestore Database → Rules**. You should see
the rules from `firestore.rules` (`allow read, write: if true` for `game`, `votes`,
`voteLog` and `characters`).

> If deploy complains that Hosting isn't set up, open **Build → Hosting** in the
> console, click **Get started** and click through (skip the CLI steps it shows),
> then run `firebase deploy` again.

After any later change to the files, run `firebase deploy` again. The HTML is served with
`Cache-Control: no-cache`, so phones get the new version on reload.

### 7. Open the control page and add photos

1. On your laptop, open `https://mmi-halloween-2026.web.app/control-k7427s.html`.
   On first load it creates `game/state` in Firestore. The header should read
   **Screen: TITLE**.
2. Under **Suspect photos**, click **Upload** for each suspect and pick a photo.
   It is resized in the browser to 400px JPEG (about 20–50 KB) and saved in Firestore.
   Portrait shots with the face in the upper third work best.

### 8. Open the vote page on a phone

1. On the control page, scroll to **Vote page**. Scan the QR code with your phone camera
   (or type the URL).
2. The phone should show **"Who killed Dr. Boddy?"**, the moon, cats and cobweb, three sections of
   buttons, and the status **"Voting opens soon…"**.
3. Tap a suspect, weapon and location. The bottom button stays disabled and reads
   "X of 3 picked", then "Voting opens soon…" until you open voting.

### 9. Open the stage page on the projector

1. On the projector laptop, open `https://mmi-halloween-2026.web.app/stage-nkddi5.html`.
2. Press **F** for full screen (or use the browser's full-screen command).
3. It shows the TITLE card. The layout is 16:9 and scales to any screen size.

### 10. Run each simulation

On the control page, **Votes** card:

1. Click **Simulate 20**. The table fills with 20 rows tagged `SIM`. Some carry a red
   **REVOTE** badge with a change count. Click a row to expand its full submission history.
2. Click **RESULTS** in the phase row. On the projector the bars grow in: the top 3 full
   guesses in pumpkin, an "Everything else" stacked grey bar, and three panels (suspect,
   weapon, location). With a mouse on the stage laptop, hover the grey stacked bar to
   see the popover.
3. Click **Clear simulated votes**. The table empties and the projector updates.
4. Repeat with **Simulate 50**, **Simulate 100** and **Simulate 200** (clear in between, or let
   them stack up). Check that the results screen stays readable at each size.
5. With about 200 votes, click **REVEAL** and confirm. On the projector: the screen dims,
   "The murderer was…" appears, three cards flip one at a time, and the reveal line appears.
   About 20 s later (or click the projector screen to skip ahead) it settles on the results
   with the correct bars in gold, everything else faded, and the "% solved all three" /
   "% fooled by Colonel Mustard" stats.
6. Click **Export CSV** and open the file. Check the columns
   `deviceId, suspect, weapon, location, revote, changes, ts`.
7. Click **Clear simulated votes**, then **Reset game to TITLE**.

The simulator produces a realistic spread: about 30% fall for the Mustard trap, about
12% solve it, and the rest are scattered across plausible near-misses and random guesses.

### 11. Full dry run of every phase

Do this once with a couple of real phones, in the room if possible.

| # | Host clicks (control page) | Projector shows | Phones show |
|---|---|---|---|
| 1 | **TITLE** | Victim intro | "Voting opens soon…" |
| 2 | **LINEUP** | Suspect 1, Colonel Mustard, large: photo, name, weapon, room. Lineup strip appears at the bottom | same |
| 3 | **Next ▶** ×6 (one per person walking out) | Suspects 2–7, one at a time. The strip fills in 1→7. The control page shows who walks next | same |
| 4 | **RULE** | Rule card | same |
| 5 | **CLUES** | Clue 1 (Mustard), large with fade-in | same |
| 6 | **Next ▶** ×4 | Clues 2–5. Earlier clues stack in the left column | same |
| 7 | Set countdown (default 2 min), then **Open voting** (or the **VOTING** phase button) | Big QR, live vote counter, countdown, all 5 clues, lineup strip | "Voting is open · 1:59 left". Vote! |
| 8 | On a phone: change your pick and vote again | Counter stays the same (latest vote counts) | "Vote locked in 🎃. Changed your mind? Vote again." |
| 9 | Check the control table | | Your device shows a red REVOTE badge with "1 change". Expand it to see both submissions |
| 10 | Wait for 0:00, or click **Close** | "Voting closed" | "Voting closed", buttons disabled, shows "Your final guess" |
| 11 | **RESULTS** | Histograms grow in | still closed, never any results |
| 12 | **REVEAL** (confirm) | Dim → "The murderer was…" → 3 card flips → reveal line → gold results + stats | still closed |
| 13 | **Reset ALL votes** (confirm), then **Reset game to TITLE** | Back to TITLE | "Voting opens soon…", their old vote is gone |

Use **+1 min** to extend a running countdown. **◀ Back** steps backwards through the lineup or clues.

### 12. Right before the party

1. Control page: **Reset ALL votes** and **Reset game to TITLE**.
2. Print the QR code (**Print QR code** on the control page) and/or leave it on the projector during VOTING.
3. Keep the control page open during voting. It closes voting in Firestore when the
   countdown hits zero. (Phones and the projector also stop at zero on their own.)
4. Line the cast up backstage **in walk order** (see [SUSPECTS.md](SUSPECTS.md)).

---

## Host solution (do not show this anywhere)

**Answer: Miss Scarlett, with the Microscope, in the Elevator.**
Reveal line: "Dr. Boddy bumped her microscope booking. For the third time."
The trap most people fall for: Colonel Mustard, Microscope, Elevator.

- Peach walked out 3rd, so her neighbors were Scarlett (2nd) and Plum (4th). If Peach
  is telling the truth, the weapon is the **microscope** or the **tape gun**.
- Peacock: it happened in the weapon owner's own room, so the **Elevator** (Scarlett's) or
  **Sheldon** (Plum's). Plum says nothing happened in Sheldon, which rules out Sheldon.
  That leaves **Microscope + Elevator**.
- Scarlett's clue ("the killer walked out before I did") points only at Mustard, the one
  person who walked before her. But Mustard's statement is consistent with everything,
  and the killer must lie. If Mustard were lying, it would have happened in the Cold Room
  with his ice scraper, but he walked out 1st, not next to Peach. So Mustard is telling
  the truth, which means **Scarlett lied, and Scarlett is the killer.**

---

## How it works

### Firestore data

| Path | Fields |
|---|---|
| `game/state` | `phase` (TITLE, LINEUP, RULE, CLUES, VOTING, RESULTS, REVEAL), `lineupIndex` (0–6), `clueIndex` (0–4), `votingOpen`, `countdownEndsAt` (ms timestamp or null), `reveal` (null, or `{suspect, weapon, location, line}` once REVEAL is pressed) |
| `characters/{id}` | `photoDataUrl` |
| `votes/{deviceId}` | `suspect, weapon, location, ts, revote, changes, sim`: one doc per device, the latest vote |
| `voteLog/{autoId}` | `deviceId, suspect, weapon, location, ts, sim`: every submission ever made |

Ids stored in votes: suspects `mustard, scarlett, peach, plum, peacock, white, green`;
weapons `icescraper, microscope, textbook, tapegun, poison, hose, pipette`;
locations `coldroom, elevator, reading, sheldon, grin, glass, flow`. The CSV export
converts them to readable names.

### Where the answer lives

Only in `control-k7427s.html` (the `ANSWER` constant). It is written to `game/state.reveal`
when the host presses REVEAL, and cleared when the host switches to any other phase.
The vote page never contains it, and neither does the stage page. The stage even
gets the reveal line from Firestore, because the line gives the answer away.

### Vote rules

- `deviceId` is a random UUID in the phone's `localStorage`.
- Each submission writes `votes/{deviceId}` (overwriting it, so the latest counts) and appends
  to `voteLog`, in one batch.
- If the new vote differs from the device's previous vote: `revote = true`, `changes + 1`.
  Resubmitting the same vote changes neither.

### Results details

- **Top accusations:** the 3 most common full guesses. "Everything else" is one stacked
  bar on the same scale: up to 6 individual guesses with ≥3 votes, then one segment for
  all 2-vote guesses, then the darkest segment for all 1-vote guesses. If more than 6
  leftover guesses have ≥3 votes, the extras share one "N more guesses with 3+ votes"
  segment placed before the 2-vote one.
- **Suspect / Weapon / Location:** top 3 plus anything with ≥6% of votes. The rest are
  collapsed into "Others (n)" with their names listed underneath.
- **"% fooled by Colonel Mustard"** = share of voters who accused Mustard as the suspect.

### Load and cost

The free Spark plan is enough: 50,000 reads, 20,000 writes and 20,000 deletes per day.
Each phone listens to just two tiny documents (`game/state` and its own vote). Every host
click costs about one read per phone (~200), so a full show is roughly 10–15k reads.
**Simulate 200** costs about 420 writes and clearing it costs about 420 deletes, so don't
run dozens of big simulations on show day. To remove any doubt, upgrade the project to
Blaze (pay as you go). A night like this costs pennies.

### Troubleshooting

- **Phone stuck on "Connecting…"**: the venue wifi may block Google APIs. Try mobile data.
  The Firestore SDK falls back to long-polling automatically on restrictive networks.
- **Phone shows "Weak signal, still sending"**: the vote is queued and retries on its own.
  Tell people to keep the page open.
- **Stage looks stale**: reload it. All state lives in Firestore, so nothing is lost.
  If you reload during REVEAL, the card flip replays.
- **Countdown looks off on one phone**: that phone's clock is wrong. Voting really closes when
  the control page closes it.
- **Changing content**: suspects, clues and text are constants near the top of each
  page's `<script>`. Keep all three files in sync and redeploy.
