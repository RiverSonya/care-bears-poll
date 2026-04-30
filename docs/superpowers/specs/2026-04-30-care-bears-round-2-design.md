# Care Bears Poll — Round 2 Design

## Context

`care-bears-poll.html` is a single-file React app (loaded via Babel standalone) that lets a team vote to match each teammate with a Care Bear. Round 1 has concluded and 9 matches are now locked in. Round 2 needs to assign bears to 6 additional teammates, using an expanded bear pool. There is also a known race condition in vote submission that overwrites concurrent votes.

This is a single-file project with no build step. All work happens inside `care-bears-poll.html`.

## Goals

1. Display the 9 round 1 winners as locked-in assignments on the front page, with elaborated bear descriptions next to each engineer.
2. Expand the bear pool with 11 new bears (10 wiki bears + Forest Friend Bear, all with engineering-themed descriptions) plus a custom Claude Bear.
3. Run a round 2 vote for 6 new teammates using the available bears.
4. Fix the race condition where simultaneous votes overwrite each other.
5. Audit colors and icons across **all** bears (locked + new) so they reflect the canonical Care Bears fur color and tummy symbol from the wiki.

## Non-goals

- Authentication / per-user access control.
- Vote editing after submission (existing Reset button stays as is).
- Any backend other than the existing JSONBin.
- Persisting round 1 voter data — round 1 is settled; the locked-in matches are hardcoded.

## Round 1 locked-in assignments

These are hardcoded as a constant in the source:

| Engineer | Bear |
|---|---|
| Ian | Tenderheart Bear |
| Javi | Funshine Bear |
| Jonathan | Cheer Bear |
| Nathan | Grumpy Bear |
| Souffiane | Bedtime Bear |
| Liam | Wish Bear |
| Sonya | Friend Bear |
| Jackie | Good Luck Bear |
| Jidesh | Love-a-Lot Bear |

The descriptions for these 9 bears get elaborated (kept in the same playful + engineering-translated style as the existing ones).

## Round 2

**Teammates to match:** Natalia, Ivan, Federico, Claude, Intern John, Megan (6 people).

**Bear pool (13 bears, all available for round 2 voting):**
- Birthday Bear (already in original list, was unassigned in round 1)
- Claude Bear (new — custom)
- Forest Friend Bear (new — wiki)
- 10 other wiki bears: Champ, Harmony, Share, Smart Heart, Brave Heart Lion, Surprise, Secret, True Heart, Wonderheart, Take Care

The 9 locked-in bears are NOT in the round 2 pool — only Birthday + Claude + Forest Friend + 10 wiki bears = 13 options for 6 people.

Each new bear needs: name, icon, color, and a two-part description: **canonical Care Bears description first** (drawn from the fandom wiki), followed by a semicolon and the engineering twist. Example (existing Bedtime Bear): *"Helps children feel safe and sleep peacefully; or helps engineers sleep peacefully by ensuring things are carefully built, well tested, and have the proper observability."*

## Canonical color and icon table

All 22 bears (9 locked + 13 in round 2 pool) use canonical Care Bears fur colors and tummy-symbol-inspired icons. The colors and icons currently in code do not all match canon — this is a full audit. New icons must be added to the file; some Task-1-added icons turn out unused and should be removed.

| # | Bear | Hex | Tummy symbol | Icon component | Status |
|---|------|-----|--------------|----------------|--------|
| 1 | Tenderheart Bear | `#C68863` | red heart | Heart | existing |
| 2 | Cheer Bear | `#FFB7C5` | rainbow | Rainbow | **new icon** |
| 3 | Funshine Bear | `#FFD93D` | smiling sun | Sun | existing |
| 4 | Grumpy Bear | `#5B9BD5` | raincloud | Cloud | existing |
| 5 | Good Luck Bear | `#7BC967` | 4-leaf clover | Clover | existing |
| 6 | Love-a-Lot Bear | `#FF85A2` | two pink hearts | Heart | existing (re-used) |
| 7 | Friend Bear | `#F4B183` | two flowers | Flower2 | existing |
| 8 | Wish Bear | `#7DCEC4` | shooting star | Star | existing |
| 9 | Bedtime Bear | `#6E8FBC` | crescent moon + star | Moon | existing |
| 10 | Birthday Bear | `#FFD93D` | cupcake with candle | Cake | **new icon** |
| 11 | Champ Bear | `#4A90E2` | trophy with star | Trophy | added in Task 1 |
| 12 | Harmony Bear | `#B58FC9` | smiling flower | Flower | **new icon** |
| 13 | Share Bear | `#C8A2C8` | two crossed lollipops | Lollipop | **new icon** |
| 14 | Smart Heart Bear | `#FFC0CB` | heart with eyes/stars | Lightbulb | added in Task 1 |
| 15 | Brave Heart Lion | `#E89B43` | red heart with crown | Crown | **new icon** |
| 16 | Surprise Bear | `#C58BB8` | star/present | PartyPopper | added in Task 1 |
| 17 | Secret Bear | `#FF85A2` | heart-shaped padlock | Lock | added in Task 1 |
| 18 | True Heart Bear | `#FFB6C1` | rainbow heart | Heart | existing (re-used) |
| 19 | Wonderheart Bear | `#FF85B5` | big heart + small hearts | Heart | existing (re-used) |
| 20 | Take Care Bear | `#B19CD9` | flower with heart center | HeartHandshake | added in Task 1 |
| 21 | Forest Friend Bear | `#9CB880` | tree | TreePine | **new icon** |
| 22 | Claude Bear | `#D97757` | brain (custom) | Brain | added in Task 1 |

**Icons added in Task 1 that turned out unused:** `Music`, `Share2`, `Shield`, `Compass`, `Telescope`. These get removed.

**New icons to add:** `Rainbow`, `Cake`, `Flower`, `Lollipop`, `Crown`, `TreePine`.

The locked bears' descriptions get elaborated to weave in their canonical tummy-symbol details where natural (e.g., Cheer Bear's rainbow, Friend Bear's two flowers, Birthday Bear's cupcake).

## Page layout

### Vote tab (front page), top-to-bottom:

1. **Header** — existing title and tagline.
2. **Tab bar** — existing Vote / Results buttons.
3. **🏆 Round 1 Champions** (new section) — grid of 9 cards, one per locked-in engineer. Each card: bear icon (filled, color-tinted), engineer name, bear name in bear color, elaborated description. Visually distinct from voting cards (e.g., gold/champion accent, "🔒 Locked" badge or similar treatment — final styling decided in implementation).
4. **Meet the Care Bears (Round 2 Pool)** — grid of the 13 round-2-available bears with icons + descriptions. Heading clarifies these are the bears available for round 2.
5. **Vote: Round 2** (existing voting UI, modified) — name input + rows for the 6 round 2 teammates. Each row shows 13 bear option buttons. Submit button.

### Results tab, top-to-bottom:

1. **🏆 Locked Assignments** — the 9 round 1 champions (always shown, not vote-dependent).
2. **⭐ Round 2 Best Matches (Optimal 1-to-1 Assignment)** — same optimal-matching logic as before, but only over the 6 new teammates and 13 available bears.
3. **Full Voting Breakdown** — only the 6 round 2 teammates.
4. **Voters** chip list — unchanged.

## Data layer

- The 9 locked-in matches live in code as a const (`lockedAssignments`) — not in JSONBin.
- JSONBin stores only round 2 votes: `{ [voterName]: { [round2Member]: bearName } }`.
- Existing round 1 vote data in the bin is now stale. **Strategy:** on first round 2 PUT, the new code overwrites the bin contents with round-2-only structure. No explicit migration needed; round 1 data is preserved in git history if anyone ever wants to recover it.
- `teamMembers` constant changes from the round 1 list to the round 2 list (`['Natalia', 'Ivan', 'Federico', 'Claude', 'Intern John', 'Megan']`).
- `careBears` constant grows: existing 10 + 10 new wiki bears + Forest Friend Bear + Claude Bear = 22 bear definitions total. The 9 locked bears stay in the array (so the Round 1 Champions section can look them up by name).
- A derived `round2Bears` array (`careBears` filtered to drop the 9 locked names) is used everywhere the round 2 UI/logic needs the available pool: the "Meet the Care Bears (Round 2 Pool)" grid, the per-member voting buttons, and the optimal-matching + breakdown computations on the Results tab. This keeps the locked bears excluded from vote counting and matching without duplicating data.

## Race condition fix

**Current bug:** `handleVote` does:
```js
const updatedVotes = { ...votes, [voterName]: selections };
PUT updatedVotes
```
Two simultaneous voters both base `updatedVotes` on a stale local `votes`, so the second PUT clobbers the first.

**Fix:** in `handleVote`, immediately before the PUT, do a fresh GET, merge the new voter into the *fresh* result, then PUT. Also update local `votes` state from the fresh GET so the UI reflects any votes that arrived during deliberation.

```js
// pseudocode
const handleVote = async () => {
  // ...validation...
  const fresh = await GET(API_URL);
  const freshVotes = fresh.record || {};
  const updatedVotes = { ...freshVotes, [voterName]: selections };
  const res = await PUT(API_URL, updatedVotes);
  if (res.ok) {
    setVotes(updatedVotes);
    setHasVoted(true);
    setCurrentTab('results');
  } else {
    alert('Error saving vote. Please try again.');
  }
};
```

JSONBin v3 has no conditional-update / If-Match support, so this is the pragmatic fix. The remaining race window is single-round-trip (~100ms), acceptable for a small team poll.

## Testing / verification

- Manual: load page, confirm Round 1 Champions grid renders all 9 bears with elaborated descriptions and correct icons/colors.
- Manual: confirm the round 2 bear pool shows exactly 13 bears (Birthday, Forest Friend, Claude, 10 wiki).
- Manual: cast a round 2 vote, confirm it appears in Results → Round 2 Best Matches.
- Manual race-condition smoke test: open page in two browser windows, prepare a vote in each, submit nearly simultaneously, confirm both voters appear in the bin (rather than one clobbering the other).
- Visual check: locked champions section is visually distinct from voting cards.

## Open implementation choices (decided in plan, not here)

- Exact visual treatment of the "locked" champion cards (gold border? badge? subtle background pattern?).
- Whether to show round 1 winners on the Vote tab too, or only on Results — the design above shows them on both for visibility, this can be revisited.
- Specific SVG paths for the new icons (resolved in Task 1 + Task 1b of the implementation plan).