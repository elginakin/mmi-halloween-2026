# A Lab Murder 🎃

A live Clue game for a department Halloween party. Seven lab members in Clue costumes
walk across the stage, each holding their "murder weapon". The projector tells the story
and shows the clues. The audience (up to ~200 people) votes on their phones for a
**suspect**, a **weapon** and a **location**. Then the projector shows the vote results,
followed by the dramatic reveal.

## The web pages

| Page | URL | Who uses it | What it does |
|---|---|---|---|
| **Vote** | https://mmi-halloween-2026.web.app/ | The audience, on their phones (via a QR code) | Pick one suspect, one weapon and one location, then lock it in. You can change your vote until voting closes. Results are never shown on phones. |
| **Stage** | https://mmi-halloween-2026.web.app/stage-nkddi5.html | The projector laptop | The big screen: story, suspect lineup, rule card, clues, voting QR code with countdown, results, reveal. It follows the control page automatically. |
| **Control** | https://mmi-halloween-2026.web.app/control-k7427s.html | The host, on a phone (or laptop) | Runs the show with one big **Next** / **◀ Back** bar that steps through every screen in order. Also opens and closes voting, uploads suspect photos, shows the live vote table, exports CSV. |
| **Crew** | https://mmi-halloween-2026.web.app/crew-okzzjf.html | Host, MC and the 7 suspects | Homepage for the crew: links to every page, the mystery and rules, the cast briefing, and the MC script. The MC script highlights the line that's on the projector right now. |

## How to run the show

1. Start from the **Crew** page. Open **Stage** on the projector laptop (press **F** for full screen), **Control** on the host's phone, and the **MC script** (on the Crew page) on the MC's laptop.
2. The host taps **Next** on Control for each screen, in order:
   **TITLE → LINEUP** (one tap per suspect who walks out) **→ RULE → CLUES** (one tap per clue) **→ VOTING → RESULTS → REVEAL**.
   The MC reads the highlighted block of the script, which follows along automatically.
3. During **VOTING**, the audience scans the QR code on the projector, picks 3 things and taps **Lock in my guess**.
4. **RESULTS** closes voting and shows the histograms. **REVEAL** flips the answer cards.

Cast briefing: [SUSPECTS.md](SUSPECTS.md) (also on the Crew page). Setup, deployment, dry run and the answer: [DEPLOY.md](DEPLOY.md).

---

## The mystery

> *Halloween night, 11:47 PM. Dr. Boddy, keeper of the department's microscope booking
> calendar, is found dead. Seven lab members were in the building. One of them is lying.*

### The suspects (in walk order)

The order they walk out is part of the puzzle.

| # | Suspect | Weapon | Their room |
|---|---|---|---|
| 1 | 🟡 Colonel Mustard | ❄️ Ice scraper | ❄️ Cold Room |
| 2 | 🔴 Miss Scarlett | 🔬 Microscope | 🛗 Elevator |
| 3 | 🍑 Miss Peach | 📕 Textbook | 📚 Reading Rooms |
| 4 | 🟣 Professor Plum | 📦 Tape gun | 🚪 Sheldon |
| 5 | 🔵 Mrs. Peacock | ☠️ Poison | ☕ Daily Grind |
| 6 | ⚪ Mrs. White | 🪢 Rubber hose | 🪟 Glass Conference Room |
| 7 | 🟢 Ms. Green | 💧 Micropipette | 🧪 Flow Core |

### The rule

> **The innocent tell the truth. The killer lies.** Remember the order they walked out.

### The clues (revealed one at a time)

1. **Colonel Mustard:** "The body was still warm. Whatever happened, it wasn't in my Cold Room."
2. **Miss Peach:** "The weapon belongs to someone who walked out right next to me."
3. **Mrs. Peacock:** "It happened on the weapon owner's own turf, in their room."
4. **Professor Plum:** "I was in Sheldon all night. Nothing happened there."
5. **Miss Scarlett:** "The killer walked out before I did."

**Who killed Dr. Boddy, with what, and where?** Vote on your phone.
