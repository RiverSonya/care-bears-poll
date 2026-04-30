# Care Bears Poll Round 2 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Update `care-bears-poll.html` to lock in the 9 round 1 matches, expand the bear pool with 12 new bears (11 from the Care Bears wiki including Forest Friend Bear + a custom Claude Bear), audit all bear colors and icons against canonical Care Bears references, run a round 2 vote for 6 new teammates, and fix a concurrent-vote race condition.

**Architecture:** All changes happen inside the single file `care-bears-poll.html`. The 9 round 1 matches are hardcoded as a `lockedAssignments` constant. The `careBears` array grows from 10 to 21 entries and the locked bears get elaborated two-part descriptions. A derived `round2Bears` array (filtered to drop the 9 locked bears) drives the round 2 voting UI and results computations. The `handleVote` function does a fresh `GET` before the `PUT` to avoid clobbering concurrent votes.

**Tech Stack:** React 18 + Babel standalone (in-browser transpilation), Tailwind CSS via CDN, JSONBin.io v3 for vote storage, lucide-style inline SVG icons. **No test framework** — verification is by opening the file in a browser. Each task ends with a browser-based verification step.

**Spec:** `docs/superpowers/specs/2026-04-30-care-bears-round-2-design.md`

---

## File Structure

Single file modifications only:
- Modify: `care-bears-poll.html`

The file's existing zones (line ranges from current `main`):
- 1–21: HTML head + body shell
- 22–110: Inline SVG icon components (Heart, Sparkles, Sun, Cloud, Clover, Flower2, Users, Gift, Star, Moon)
- 112–241: Component setup (state, constants, data loading, vote-counting helpers)
- 243–285: `getOptimalMatching` + loading state
- 287–530: JSX render (header, tab bar, vote tab, results tab)
- 533: `ReactDOM.render`

New code lands in four zones:
1. **Icons zone** (after line 110, before `const CareBearsPoll = () => {`): 11 new SVG components.
2. **Constants zone** (inside the component, around the existing `careBears`/`teamMembers`): elaborated descriptions on the 9 locked bears, 11 new bear entries, new `lockedAssignments`, updated `teamMembers`, derived `round2Bears`.
3. **JSX zone** (Vote tab + Results tab): new "Round 1 Champions" section, "Meet the Care Bears" + voting buttons + results sections updated to use `round2Bears`, and a "Locked Assignments" section on Results.
4. **Logic zone** (`handleVote`): fresh GET before PUT.

---

## Task 1: Add 11 new SVG icon components

**Files:**
- Modify: `care-bears-poll.html` — insert after the `Moon` component (after line 110), before `const CareBearsPoll = () => {` (line 112).

All 11 icons follow the existing pattern (stateless SVG, 24×24 viewBox, stroke-based, `size`/`color`/`fill`/`opacity` props). They are pulled from the Lucide icon set, matching the style already in the file.

- [ ] **Step 1: Insert the 11 new icon components**

After line 110 (after the `Moon` component closing parens `);`), insert:

```jsx
    const Trophy = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M6 9H4.5a2.5 2.5 0 0 1 0-5H6"/>
        <path d="M18 9h1.5a2.5 2.5 0 0 0 0-5H18"/>
        <path d="M4 22h16"/>
        <path d="M10 14.66V17c0 .55-.47.98-.97 1.21C7.85 18.75 7 20.24 7 22"/>
        <path d="M14 14.66V17c0 .55.47.98.97 1.21C16.15 18.75 17 20.24 17 22"/>
        <path d="M18 2H6v7a6 6 0 0 0 12 0V2Z"/>
      </svg>
    );

    const Music = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M9 18V5l12-2v13"/>
        <circle cx="6" cy="18" r="3"/>
        <circle cx="18" cy="16" r="3"/>
      </svg>
    );

    const Share2 = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <circle cx="18" cy="5" r="3"/>
        <circle cx="6" cy="12" r="3"/>
        <circle cx="18" cy="19" r="3"/>
        <line x1="8.59" y1="13.51" x2="15.42" y2="17.49"/>
        <line x1="15.41" y1="6.51" x2="8.59" y2="11.49"/>
      </svg>
    );

    const Lightbulb = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M9 18h6"/>
        <path d="M10 22h4"/>
        <path d="M15.09 14c.18-.98.65-1.74 1.41-2.5A4.65 4.65 0 0 0 18 8 6 6 0 0 0 6 8c0 1 .23 2.23 1.5 3.5A4.61 4.61 0 0 1 8.91 14"/>
      </svg>
    );

    const Shield = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M20 13c0 5-3.5 7.5-7.66 8.95a1 1 0 0 1-.67-.01C7.5 20.5 4 18 4 13V6a1 1 0 0 1 1-1c2 0 4.5-1.2 6.24-2.72a1.17 1.17 0 0 1 1.52 0C14.51 3.81 17 5 19 5a1 1 0 0 1 1 1z"/>
      </svg>
    );

    const PartyPopper = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M5.8 11.3 2 22l10.7-3.79"/>
        <path d="M4 3h.01"/>
        <path d="M22 8h.01"/>
        <path d="M15 2h.01"/>
        <path d="M22 20h.01"/>
        <path d="m22 2-2.24.75a2.9 2.9 0 0 0-1.96 3.12c.1.86-.57 1.63-1.45 1.63h-.38c-.86 0-1.6.6-1.76 1.44L14 10"/>
        <path d="m22 13-1.99-.46a2.91 2.91 0 0 0-3.13 1.97c-.21.85-1.21 1.21-1.91.71l-.4-.3c-.71-.51-1.7-.16-1.97.65L12 17"/>
        <path d="M18 18a4 4 0 0 0-2-2 8 8 0 0 0-7-7 4 4 0 0 0-2-2"/>
      </svg>
    );

    const Lock = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <rect width="18" height="11" x="3" y="11" rx="2" ry="2"/>
        <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
      </svg>
    );

    const Compass = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <circle cx="12" cy="12" r="10"/>
        <polygon points="16.24 7.76 14.12 14.12 7.76 16.24 9.88 9.88 16.24 7.76"/>
      </svg>
    );

    const Telescope = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="m10.065 12.493-6.18 1.318a.934.934 0 0 1-1.108-.702l-.537-2.15a1.07 1.07 0 0 1 .691-1.265l13.504-4.44"/>
        <path d="m13.56 11.747 4.332-.924"/>
        <path d="m16 21-3.105-6.21"/>
        <path d="M16.485 5.94a2 2 0 0 1 1.455-2.425l1.09-.272a1 1 0 0 1 1.212.727l1.515 6.06a1 1 0 0 1-.727 1.213l-1.09.272a2 2 0 0 1-2.425-1.455z"/>
        <path d="m6.158 8.633 1.114 4.456"/>
        <path d="m8 21 3.105-6.21"/>
        <circle cx="12" cy="13" r="2"/>
      </svg>
    );

    const HeartHandshake = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M19 14c1.49-1.46 3-3.21 3-5.5A5.5 5.5 0 0 0 16.5 3c-1.76 0-3 .5-4.5 2-1.5-1.5-2.74-2-4.5-2A5.5 5.5 0 0 0 2 8.5c0 2.3 1.5 4.05 3 5.5l7 7Z"/>
        <path d="M12 5 9.04 7.96a2.17 2.17 0 0 0 0 3.08v0c.82.82 2.13.85 3 .07l2.07-1.9a2.82 2.82 0 0 1 3.79 0l2.96 2.66"/>
        <path d="m18 15-2-2"/>
        <path d="m15 18-2-2"/>
      </svg>
    );

    const Brain = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M9.5 2A2.5 2.5 0 0 1 12 4.5v15a2.5 2.5 0 0 1-4.96.44 2.5 2.5 0 0 1-2.96-3.08 3 3 0 0 1-.34-5.58 2.5 2.5 0 0 1 1.32-4.24 2.5 2.5 0 0 1 1.98-3A2.5 2.5 0 0 1 9.5 2Z"/>
        <path d="M14.5 2A2.5 2.5 0 0 0 12 4.5v15a2.5 2.5 0 0 0 4.96.44 2.5 2.5 0 0 0 2.96-3.08 3 3 0 0 0 .34-5.58 2.5 2.5 0 0 0-1.32-4.24 2.5 2.5 0 0 0-1.98-3A2.5 2.5 0 0 0 14.5 2Z"/>
      </svg>
    );
```

- [ ] **Step 2: Verify in browser**

Open `care-bears-poll.html` in a browser. The page should still load (no syntax errors). Open DevTools console — there should be no React/Babel errors. The visible page is unchanged at this point because the new icons aren't used yet. If the page is blank, there's a JSX/JS error in the new components — check the browser console.

- [ ] **Step 3: Commit**

```bash
git add care-bears-poll.html
git commit -m "Add SVG icon components for round 2 bears"
```

---

## Task 1b: Replace unused icons with canonical tummy-symbol icons

After a canonical-accuracy audit (see spec § "Canonical color and icon table"), 5 of the icons added in Task 1 turned out not to match any canonical Care Bears tummy symbol and are removed. 6 new canonical-tummy-symbol icons are added in their place.

**Files:**
- Modify: `care-bears-poll.html` — in the icon block between `Moon` (around line 110 of original) and `const CareBearsPoll = () => {` (after Task 1, this block now ends around line 208).

**Removed (added in Task 1, unused after canonical audit):**
- `Music`, `Share2`, `Shield`, `Compass`, `Telescope`

**Added (canonical tummy symbols):**
- `Rainbow` (Cheer Bear)
- `Cake` (Birthday Bear — cupcake)
- `Flower` (Harmony Bear — single smiling flower; distinct from existing `Flower2`)
- `Lollipop` (Share Bear — two crossed lollipops)
- `Crown` (Brave Heart Lion — heart with crown)
- `TreePine` (Forest Friend Bear)

- [ ] **Step 1: Remove the 5 unused icon components**

In `care-bears-poll.html`, find and delete the entire component definitions for `Music`, `Share2`, `Shield`, `Compass`, and `Telescope` (added in Task 1). Each is in the form:

```jsx
    const Music = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        ...
      </svg>
    );
```

Delete the whole block (the `const Foo = ...);` plus the trailing blank line) for each of the 5 names.

- [ ] **Step 2: Add the 6 new icon components**

Insert these 6 new components into the same icon block (anywhere between `Moon` and the start of `CareBearsPoll`; placement order is cosmetic, alphabetical or appearance order both fine). Use the same 4-space indentation as existing icon components.

```jsx
    const Rainbow = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M22 17a10 10 0 0 0-20 0"/>
        <path d="M6 17a6 6 0 0 1 12 0"/>
        <path d="M10 17a2 2 0 0 1 4 0"/>
      </svg>
    );

    const Cake = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M20 21v-8a2 2 0 0 0-2-2H6a2 2 0 0 0-2 2v8"/>
        <path d="M4 16s.5-1 2-1 2.5 2 4 2 2.5-2 4-2 2.5 2 4 2 2-1 2-1"/>
        <path d="M2 21h20"/>
        <path d="M7 8v3"/>
        <path d="M12 8v3"/>
        <path d="M17 8v3"/>
        <path d="M7 4h.01"/>
        <path d="M12 4h.01"/>
        <path d="M17 4h.01"/>
      </svg>
    );

    const Flower = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <circle cx="12" cy="12" r="3"/>
        <path d="M12 16.5A4.5 4.5 0 1 1 7.5 12 4.5 4.5 0 1 1 12 7.5a4.5 4.5 0 1 1 4.5 4.5 4.5 4.5 0 1 1-4.5 4.5"/>
      </svg>
    );

    const Lollipop = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <circle cx="12" cy="9" r="6"/>
        <path d="M12 15v6"/>
        <path d="M12 5a4 4 0 0 0-2 7.5"/>
      </svg>
    );

    const Crown = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="M11.562 3.266a.5.5 0 0 1 .876 0L15.39 8.87a1 1 0 0 0 1.516.294L21.183 5.5a.5.5 0 0 1 .798.519l-2.834 10.246a1 1 0 0 1-.956.734H5.81a1 1 0 0 1-.957-.734L2.02 6.02a.5.5 0 0 1 .798-.519l4.276 3.664a1 1 0 0 0 1.516-.294z"/>
        <path d="M5 21h14"/>
      </svg>
    );

    const TreePine = ({ size = 24, color = "currentColor", fill = "none", opacity = 1 }) => (
      <svg width={size} height={size} viewBox="0 0 24 24" fill={fill} stroke={color} strokeWidth="2" opacity={opacity}>
        <path d="m17 14 3 3.3a1 1 0 0 1-.7 1.7H4.7a1 1 0 0 1-.7-1.7L7 14h-.3a1 1 0 0 1-.7-1.7L9 9h-.2A1 1 0 0 1 8 7.3L11.3 3a1 1 0 0 1 1.4 0L16 7.3a1 1 0 0 1-.8 1.7H15l3 3.3a1 1 0 0 1-.7 1.7H17Z"/>
        <path d="M12 22v-3"/>
      </svg>
    );
```

- [ ] **Step 3: Verify in browser**

Open `care-bears-poll.html`. Page should still load with no console errors. The 5 unused icons (Music/Share2/Shield/Compass/Telescope) are gone; the 6 new ones are present but not yet used (Task 2 will reference them).

- [ ] **Step 4: Commit**

```bash
git add care-bears-poll.html
git commit -m "Replace unused icons with canonical tummy-symbol icons"
```

---

## Task 2: Update `careBears` array — canonical colors, canonical icons, 12 new bears (incl. Forest Friend)

**Files:**
- Modify: `care-bears-poll.html` lines 125–136 of the original (`careBears = [...]` array — line numbers may have shifted after Tasks 1 and 1b; locate by content).

The new array has 22 entries. All bears use **canonical Care Bears fur colors and tummy-symbol-inspired icons** (see spec § "Canonical color and icon table"). The 9 locked bears keep their identity but get updated colors, icons (where canon differs), and richer two-part descriptions. The 13 round 2 bears (Birthday + Forest Friend + 10 wiki + Claude) are added/updated to round out the array.

- [ ] **Step 1: Replace the `careBears` array**

Find the `const careBears = [ ... ];` block and replace with:

```jsx
      const careBears = [
        { name: 'Tenderheart Bear', icon: Heart, color: '#C68863', description: 'The unofficial leader of the Care Bears, with a red heart on his tan tummy, focused on caring and spreading love; the team lead who keeps everyone aligned, unblocks people, and treats teammates with empathy.' },
        { name: 'Cheer Bear', icon: Rainbow, color: '#FFB7C5', description: 'Known for her optimism and spreading happiness wherever she goes, with a rainbow on her pink tummy; the relentless cheerleader who celebrates every green CI build and keeps morale up during long debugging sessions.' },
        { name: 'Funshine Bear', icon: Sun, color: '#FFD93D', description: 'Represents joy and fun and brings sunshine wherever he goes, with a smiling sun on his yellow tummy; and yes - new features can be fun! So can jokes, memes, gifs, and entertaining meetings.' },
        { name: 'Grumpy Bear', icon: Cloud, color: '#5B9BD5', description: 'Relatable for his authentic grumpiness, with a raincloud on his blue tummy, even though he secretly cares deeply; important for helping people not bypass emotions or potential technical issues, and for asking "are we sure about this?" before merging.' },
        { name: 'Good Luck Bear', icon: Clover, color: '#7BC967', description: 'Brings good fortune and shamrocks to everyone around him, with a four-leaf clover on his green tummy; including smooth deploys, reduced tech debt, and the kind of luck that comes from solid fundamentals.' },
        { name: 'Love-a-Lot Bear', icon: Heart, color: '#FF85A2', description: 'Celebrates all kinds of affection, with two pink hearts on her pink tummy; the engineer who hypes everyone\'s PRs, leaves thoughtful review comments, and remembers everyone\'s birthday.' },
        { name: 'Friend Bear', icon: Flower2, color: '#F4B183', description: 'Emphasizes friendship and being a confidant, with two yellow daisies on her peach tummy; always ready to rubber duck or help debug an issue, the first DM you send when stuck.' },
        { name: 'Wish Bear', icon: Star, color: '#7DCEC4', description: 'Encourages dreams and wishes upon a star, with a shooting star on her turquoise tummy; the engineer always thinking about ways to improve our system and process, sketching the better future state.' },
        { name: 'Bedtime Bear', icon: Moon, color: '#6E8FBC', description: 'Helps children feel safe and sleep peacefully, with a crescent moon and star on his blue tummy; or helps engineers sleep peacefully by ensuring things are carefully built, well tested, and have the proper observability.' },
        { name: 'Birthday Bear', icon: Cake, color: '#FFD93D', description: 'Celebrates birthdays and special days, with a candle-lit cupcake on her yellow tummy; including celebrating effort, good work, successful releases, and new features.' },
        { name: 'Champ Bear', icon: Trophy, color: '#4A90E2', description: 'An athletic blue bear with a gold trophy and star on his tummy who celebrates good sportsmanship and personal achievement; the engineer who takes pride in shipping, runs benchmarks, and competes mostly with their own past self.' },
        { name: 'Harmony Bear', icon: Flower, color: '#B58FC9', description: 'Brings a sense of balance and helps friends get along, with a smiling flower on her purple tummy; the connector who facilitates conflict resolution, runs great pair-programming sessions, and senses when team dynamics need attention.' },
        { name: 'Share Bear', icon: Lollipop, color: '#C8A2C8', description: 'Believes the best way to enjoy something is to share it with others, with two crossed lollipops on her lilac tummy; the documentarian who writes the README nobody asked for but everyone uses, and who genuinely loves teaching.' },
        { name: 'Smart Heart Bear', icon: Lightbulb, color: '#FFC0CB', description: 'A clever pink Care Bear who loves learning and uses her brains to help her friends, with a heart full of stars on her tummy; the deep-dives engineer who roots out the actual cause instead of papering over symptoms.' },
        { name: 'Brave Heart Lion', icon: Crown, color: '#E89B43', description: 'A courageous golden lion among the Care Bear cousins, with a red heart wearing a crown on his tummy, willing to face scary challenges; the engineer who volunteers for the gnarly refactor, the legacy code with no tests, and the migration nobody else wants.' },
        { name: 'Surprise Bear', icon: PartyPopper, color: '#C58BB8', description: 'Loves the joy of unexpected delights, with a star tummy symbol that lights up at the unexpected; the engineer who doesn\'t panic when prod surprises us at 2am, and who finds edge cases everyone else missed.' },
        { name: 'Secret Bear', icon: Lock, color: '#FF85A2', description: 'Trustworthy and discreet, a pink Care Bear who keeps friends\' secrets safe with a heart-shaped padlock on his tummy; the security-minded engineer who handles credentials carefully, threat-models early, and never logs the access token.' },
        { name: 'True Heart Bear', icon: Heart, color: '#FFB6C1', description: 'The leader of the Care Bear cubs, with a rainbow heart symbolizing sincerity and integrity; the senior engineer who runs blameless postmortems, gives honest reviews, and tells you the unvarnished truth even when it\'s hard.' },
        { name: 'Wonderheart Bear', icon: Heart, color: '#FF85B5', description: 'Filled with curiosity and wonder, with a big heart surrounded by little hearts on her pink tummy; the engineer who is always evaluating new tools, prototyping wild ideas in spare time, and asking "what if we tried...?"' },
        { name: 'Take Care Bear', icon: HeartHandshake, color: '#B19CD9', description: 'A caring lavender Care Bear who looks out for friends when they\'re feeling down or under the weather, with a flower-and-heart on her tummy; the on-call hero who supports teammates through outages and notices when someone\'s burning out.' },
        { name: 'Forest Friend Bear', icon: TreePine, color: '#9CB880', description: 'A nature-loving Care Bear with a tree on her green tummy who watches over forest animals and growing things; the engineer who treats the codebase like a living ecosystem - pruning carefully, planting durable patterns, and checking the health of dependencies before they rot.' },
        { name: 'Claude Bear', icon: Brain, color: '#D97757', description: 'A new arrival to the Care Bear family who loves reading, thinking out loud, and being helpful; the thoughtful pair-programmer who reads diffs carefully, asks the right clarifying questions, and tries hard not to ship anything you didn\'t ask for.' }
      ];
```

- [ ] **Step 2: Verify in browser**

Open `care-bears-poll.html`. The "Meet the Care Bears" section on the Vote tab should now show 22 bears with canonical fur colors. Locked bears that previously had non-canonical colors (Tenderheart was hot pink, now tan; Friend was orange, now peach; etc.) and icons (Cheer used Sparkles, now Rainbow; Birthday used Gift, now Cake; Friend used Users, now Flower2) reflect canon. Forest Friend Bear renders with the TreePine icon. No console errors.

- [ ] **Step 3: Commit**

```bash
git add care-bears-poll.html
git commit -m "Update careBears with canonical colors, canonical icons, and 12 new bears"
```

---

## Task 3: Add `lockedAssignments`, update `teamMembers`, add `round2Bears`

**Files:**
- Modify: `care-bears-poll.html` line 138 (`const teamMembers = [...]`).

After this task the data layer reflects the round 2 reality: a static map of locked engineer→bear assignments, the new round 2 voter list, and a derived list of bears available for round 2.

- [ ] **Step 1: Replace `teamMembers` with the new constants block**

Find line 138:

```jsx
      const teamMembers = ['Ian', 'Liam', 'Jackie', 'Javi', 'Jonathan', 'Nathan', 'Sonya', 'Souffiane'];
```

Replace with:

```jsx
      const lockedAssignments = [
        { member: 'Ian', bear: 'Tenderheart Bear' },
        { member: 'Javi', bear: 'Funshine Bear' },
        { member: 'Jonathan', bear: 'Cheer Bear' },
        { member: 'Nathan', bear: 'Grumpy Bear' },
        { member: 'Souffiane', bear: 'Bedtime Bear' },
        { member: 'Liam', bear: 'Wish Bear' },
        { member: 'Sonya', bear: 'Friend Bear' },
        { member: 'Jackie', bear: 'Good Luck Bear' },
        { member: 'Jidesh', bear: 'Love-a-Lot Bear' }
      ];

      const lockedBearNames = new Set(lockedAssignments.map(a => a.bear));

      const teamMembers = ['Natalia', 'Ivan', 'Federico', 'Claude', 'Intern John', 'Megan'];

      const round2Bears = careBears.filter(b => !lockedBearNames.has(b.name));
```

- [ ] **Step 2: Verify in browser**

Open `care-bears-poll.html`. The Vote tab's voting form at the bottom should now list 6 rows: Natalia, Ivan, Federico, Claude, Intern John, Megan. Each row's bear-button grid still shows all 21 bears (this is fine — task 6 will narrow it). No console errors.

- [ ] **Step 3: Commit**

```bash
git add care-bears-poll.html
git commit -m "Add lockedAssignments and round 2 team list"
```

---

## Task 4: Add "Round 1 Champions" section to Vote tab

**Files:**
- Modify: `care-bears-poll.html` — insert a new section inside the `currentTab === 'vote'` block, between the existing "Meet the Care Bears" `</div>` (around line 340) and the voting form `<div className="bg-white rounded-xl shadow-lg p-8">` (around line 342).

Visual treatment: gold/amber border with a `🔒 Locked` badge, to clearly distinguish from voting cards. Renders one card per `lockedAssignments` entry, looking up the bear from `careBears` by name.

- [ ] **Step 1: Insert the Round 1 Champions section**

Find this exact span (around lines 320–322):

```jsx
            {currentTab === 'vote' && (
              <div>
                <div className="bg-white rounded-xl shadow-lg p-6 mb-8">
                  <h2 className="text-3xl font-bold text-purple-600 mb-6 text-center">Meet the Care Bears</h2>
```

Immediately before the inner `<div className="bg-white rounded-xl shadow-lg p-6 mb-8">`, insert a new champions block, so the structure becomes:

```jsx
            {currentTab === 'vote' && (
              <div>
                <div className="bg-white rounded-xl shadow-lg p-6 mb-8 border-4 border-amber-300">
                  <h2 className="text-3xl font-bold text-amber-600 mb-2 text-center">🏆 Round 1 Champions 🏆</h2>
                  <p className="text-center text-gray-600 mb-6">These bears are locked in from our first vote.</p>
                  <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                    {lockedAssignments.map(({ member, bear }) => {
                      const bearData = careBears.find(b => b.name === bear);
                      const Icon = bearData.icon;
                      return (
                        <div
                          key={member}
                          className="border-2 rounded-xl p-4 bg-gradient-to-br from-amber-50 to-yellow-50"
                          style={{ borderColor: bearData.color }}
                        >
                          <div className="flex items-center gap-3 mb-2">
                            <Icon size={32} color={bearData.color} fill={bearData.color} opacity={0.3} />
                            <div>
                              <div className="font-bold text-lg text-gray-800">{member}</div>
                              <div className="text-sm font-semibold" style={{ color: bearData.color }}>{bear}</div>
                            </div>
                            <span className="ml-auto text-xs font-bold bg-amber-200 text-amber-800 px-2 py-1 rounded-full">🔒 Locked</span>
                          </div>
                          <p className="text-sm text-gray-600">{bearData.description}</p>
                        </div>
                      );
                    })}
                  </div>
                </div>

                <div className="bg-white rounded-xl shadow-lg p-6 mb-8">
                  <h2 className="text-3xl font-bold text-purple-600 mb-6 text-center">Meet the Care Bears</h2>
```

(The first opening `<div className="bg-white rounded-xl shadow-lg p-6 mb-8">` from the original got swapped for the new champions block; the original "Meet the Care Bears" div now starts after the champions block. Be careful to only have ONE `<div>` open per section — don't accidentally nest.)

- [ ] **Step 2: Verify in browser**

Reload `care-bears-poll.html` in the browser, Vote tab. You should see a new gold-bordered "🏆 Round 1 Champions 🏆" section at the top with 9 cards (one per engineer). Each card shows the bear icon, the engineer name, the bear name colored with the bear's color, a "🔒 Locked" badge, and the elaborated description. Below it, the original "Meet the Care Bears" section still renders.

- [ ] **Step 3: Commit**

```bash
git add care-bears-poll.html
git commit -m "Add Round 1 Champions section to Vote tab"
```

---

## Task 5: Update "Meet the Care Bears" section to use `round2Bears`

**Files:**
- Modify: `care-bears-poll.html` — the "Meet the Care Bears" block from the previous task.

The "Meet the Care Bears" grid currently iterates `careBears`. After this task it iterates `round2Bears` (13 bears) and the heading clarifies these are the bears available for round 2.

- [ ] **Step 1: Update the heading and the iteration source**

Find (in the section after the champions block):

```jsx
                <div className="bg-white rounded-xl shadow-lg p-6 mb-8">
                  <h2 className="text-3xl font-bold text-purple-600 mb-6 text-center">Meet the Care Bears</h2>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                    {careBears.map((bear) => {
```

Replace with:

```jsx
                <div className="bg-white rounded-xl shadow-lg p-6 mb-8">
                  <h2 className="text-3xl font-bold text-purple-600 mb-2 text-center">Meet the Care Bears</h2>
                  <p className="text-center text-gray-600 mb-6">Bears available for Round 2 voting.</p>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                    {round2Bears.map((bear) => {
```

- [ ] **Step 2: Verify in browser**

Reload. The "Meet the Care Bears" section now shows exactly 13 bears: Birthday Bear (was unassigned in round 1) plus the 12 new bears (Champ, Harmony, Share, Smart Heart, Brave Heart Lion, Surprise, Secret, True Heart, Wonderheart, Take Care, Forest Friend, Claude). The 9 locked bears (Tenderheart, Cheer, Funshine, Grumpy, Good Luck, Love-a-Lot, Friend, Wish, Bedtime) are NOT shown here. Verify by counting: 13 cards.

- [ ] **Step 3: Commit**

```bash
git add care-bears-poll.html
git commit -m "Limit Meet the Care Bears to round 2 pool"
```

---

## Task 6: Update voting UI to use `round2Bears`

**Files:**
- Modify: `care-bears-poll.html` — the voting form's per-member bear-button grid (around line 372 in the original, currently `{careBears.map((bear) => {`).

The voting form's 6 per-member rows each render a button per available bear. Currently iterates all 22 bears; should iterate the 13 round 2 bears.

- [ ] **Step 1: Update the iteration source for voting buttons**

Find:

```jsx
                              <div className="grid grid-cols-2 md:grid-cols-5 gap-2">
                                {careBears.map((bear) => {
                                  const BearIcon = bear.icon;
                                  const isSelected = selections[member] === bear.name;
                                  return (
                                    <button
                                      key={bear.name}
                                      onClick={() => setSelections({ ...selections, [member]: bear.name })}
```

Replace `{careBears.map((bear) => {` with `{round2Bears.map((bear) => {`. Leave everything else unchanged.

- [ ] **Step 2: Verify in browser**

Reload, Vote tab. Each of the 6 round 2 voting rows (Natalia, Ivan, Federico, Claude, Intern John, Megan) now shows exactly 13 bear-button options instead of 22. Selecting one and seeing it highlight should still work. The Submit Votes button should still be present at the bottom.

- [ ] **Step 3: Commit**

```bash
git add care-bears-poll.html
git commit -m "Restrict voting buttons to round 2 bear pool"
```

---

## Task 7: Add "Locked Assignments" section to Results tab

**Files:**
- Modify: `care-bears-poll.html` — insert at the top of the `currentTab === 'results'` block (around line 425), before the existing `<div className="bg-white rounded-xl shadow-lg p-8">` that contains "⭐ Best Matches".

This section is always visible (not vote-dependent), so it sits before the existing optimal-matching grid. Visually mirrors the champions section on the Vote tab (gold-bordered, locked badge per card).

- [ ] **Step 1: Insert the Locked Assignments section at the top of the Results tab**

Find:

```jsx
            {currentTab === 'results' && (
              <div className="space-y-8">
                <div className="bg-white rounded-xl shadow-lg p-8">
                  <h2 className="text-3xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-pink-500 to-purple-500 mb-6 text-center">
                    ⭐ Best Matches (Optimal 1-to-1 Assignment) ⭐
                  </h2>
```

Replace with:

```jsx
            {currentTab === 'results' && (
              <div className="space-y-8">
                <div className="bg-white rounded-xl shadow-lg p-8 border-4 border-amber-300">
                  <h2 className="text-3xl font-bold text-amber-600 mb-2 text-center">🏆 Round 1 Locked Assignments 🏆</h2>
                  <p className="text-center text-gray-600 mb-6">Settled from our first vote.</p>
                  <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4">
                    {lockedAssignments.map(({ member, bear }) => {
                      const bearData = careBears.find(b => b.name === bear);
                      const Icon = bearData.icon;
                      return (
                        <div
                          key={member}
                          className="border-4 rounded-xl p-4 text-center bg-gradient-to-br from-amber-50 to-yellow-50"
                          style={{ borderColor: bearData.color }}
                        >
                          <Icon size={48} color={bearData.color} className="mx-auto mb-3" fill={bearData.color} opacity={0.3} />
                          <div className="font-bold text-xl text-gray-800 mb-1">{member}</div>
                          <div className="text-sm font-semibold mb-2" style={{ color: bearData.color }}>{bear}</div>
                          <div className="inline-block px-3 py-1 bg-amber-200 text-amber-800 rounded-full text-xs font-bold">🔒 Locked</div>
                        </div>
                      );
                    })}
                  </div>
                </div>

                <div className="bg-white rounded-xl shadow-lg p-8">
                  <h2 className="text-3xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-pink-500 to-purple-500 mb-6 text-center">
                    ⭐ Round 2 Best Matches (Optimal 1-to-1 Assignment) ⭐
                  </h2>
```

(Note the title also changed from "Best Matches" to "Round 2 Best Matches" for clarity.)

- [ ] **Step 2: Verify in browser**

Reload, switch to the Results tab. The first card section is now "🏆 Round 1 Locked Assignments 🏆" with 9 gold-bordered cards (Ian/Tenderheart, Javi/Funshine, etc.). Below it is "⭐ Round 2 Best Matches" — which currently shows zero or stale matches because there are no round 2 votes yet (or shows old round 1 voters' selections — that's fine, it'll be fixed by Task 8 when matching uses round2Bears + new teamMembers).

- [ ] **Step 3: Commit**

```bash
git add care-bears-poll.html
git commit -m "Add Round 1 Locked Assignments section to Results tab"
```

---

## Task 8: Update Results matching + breakdown to use `round2Bears`

**Files:**
- Modify: `care-bears-poll.html` — the helper functions `getVoteCounts` (around lines 223–241) and `getOptimalMatching` (around lines 243–277).

Currently both helpers iterate `careBears`. Since `teamMembers` is already round 2, only the bear iteration needs to change. After this task, the optimal matching considers only the 13 round 2 bears.

- [ ] **Step 1: Update `getVoteCounts` to iterate `round2Bears`**

Find:

```jsx
      const getVoteCounts = () => {
        const counts = {};
        teamMembers.forEach(member => {
          counts[member] = {};
          careBears.forEach(bear => {
            counts[member][bear.name] = 0;
          });
        });
```

Replace `careBears.forEach(bear => {` with `round2Bears.forEach(bear => {`.

- [ ] **Step 2: Update `getOptimalMatching` to iterate `round2Bears`**

Find:

```jsx
        const pairs = [];
        teamMembers.forEach(member => {
          careBears.forEach(bear => {
            pairs.push({
              member,
              bear: bear.name,
              votes: counts[member][bear.name]
            });
          });
        });
```

Replace `careBears.forEach(bear => {` with `round2Bears.forEach(bear => {`.

- [ ] **Step 3: Verify in browser**

Reload, Results tab. The "⭐ Round 2 Best Matches" grid should display nothing (if the bin is empty) or only round 2 votes if any have been cast. If there are stale round 1 votes in the bin, the matching should still respect: (a) only the 6 round 2 members, and (b) only the 13 round 2 bears. The "Full Voting Breakdown" should list only the 6 round 2 members.

- [ ] **Step 4: Commit**

```bash
git add care-bears-poll.html
git commit -m "Limit results matching and breakdown to round 2 bears"
```

---

## Task 9: Fix race condition in `handleVote`

**Files:**
- Modify: `care-bears-poll.html` lines 168–205 (the `handleVote` function).

Replace the stale-state PUT with a fresh-GET-then-PUT pattern. The local `votes` state is also refreshed from the GET so the UI reflects intervening writes.

- [ ] **Step 1: Update `handleVote`**

Find this block (lines 168–205):

```jsx
      const handleVote = async () => {
        if (!voterName.trim()) {
          alert('Please enter your name!');
          return;
        }

        if (Object.keys(selections).length !== teamMembers.length) {
          alert('Please match all team members with Care Bears!');
          return;
        }

        try {
          const updatedVotes = {
            ...votes,
            [voterName]: selections
          };

          const response = await fetch(API_URL, {
            method: 'PUT',
            headers: {
              'Content-Type': 'application/json',
              'X-Master-Key': API_KEY
            },
            body: JSON.stringify(updatedVotes)
          });

          if (response.ok) {
            setVotes(updatedVotes);
            setHasVoted(true);
            setCurrentTab('results');
          } else {
            alert('Error saving vote. Please try again.');
          }
        } catch (error) {
          alert('Error saving vote. Please try again.');
          console.error(error);
        }
      };
```

Replace with:

```jsx
      const handleVote = async () => {
        if (!voterName.trim()) {
          alert('Please enter your name!');
          return;
        }

        if (Object.keys(selections).length !== teamMembers.length) {
          alert('Please match all team members with Care Bears!');
          return;
        }

        try {
          const freshResponse = await fetch(API_URL, {
            method: 'GET',
            headers: {
              'X-Master-Key': API_KEY
            }
          });

          let freshVotes = {};
          if (freshResponse.ok) {
            const data = await freshResponse.json();
            freshVotes = data.record || {};
          } else {
            alert('Error reading current votes. Please try again.');
            return;
          }

          const updatedVotes = {
            ...freshVotes,
            [voterName]: selections
          };

          const response = await fetch(API_URL, {
            method: 'PUT',
            headers: {
              'Content-Type': 'application/json',
              'X-Master-Key': API_KEY
            },
            body: JSON.stringify(updatedVotes)
          });

          if (response.ok) {
            setVotes(updatedVotes);
            setHasVoted(true);
            setCurrentTab('results');
          } else {
            alert('Error saving vote. Please try again.');
          }
        } catch (error) {
          alert('Error saving vote. Please try again.');
          console.error(error);
        }
      };
```

- [ ] **Step 2: Verify in browser (single-user smoke test)**

Reload. On the Vote tab, enter a name (e.g., "TestVoter"), pick a bear for each of the 6 members, click Submit Votes. You should land on the Results tab with the vote counted. The "Round 2 Best Matches" grid should reflect the new vote.

- [ ] **Step 3: Verify race condition fix (two-window test)**

Open the page in two separate browser windows (or one window + one private/incognito window). In window A, enter "Voter A", pick bears, but DO NOT submit yet. In window B, enter "Voter B", pick bears, and submit. Then switch back to window A and submit. After both submissions, reload either window — both Voter A and Voter B should appear in the "Voters" chip list at the bottom of the Results tab, and both votes should contribute to the breakdown. (Before the fix, only the second submitter would have shown up.)

- [ ] **Step 4: Commit**

```bash
git add care-bears-poll.html
git commit -m "Fix race condition in handleVote with fresh GET before PUT"
```

---

## Task 10: Final end-to-end verification

This task is verification only — no code changes. The goal is to confirm the page works as a whole and the spec is satisfied. If any check fails, file a follow-up task to fix it.

- [ ] **Step 1: Vote tab — top-down visual check**

Open `care-bears-poll.html`. On the Vote tab, confirm:
- Header and tabs render correctly.
- "🏆 Round 1 Champions 🏆" gold-bordered section shows 9 cards (Ian, Javi, Jonathan, Nathan, Souffiane, Liam, Sonya, Jackie, Jidesh). Each has icon, engineer name, bear name in bear color, "🔒 Locked" badge, and elaborated description.
- "Meet the Care Bears" section shows exactly 13 bears (Birthday + Forest Friend + 11 new). All icons render. Descriptions follow two-part style.
- Voting form has 6 rows (Natalia, Ivan, Federico, Claude, Intern John, Megan). Each row shows 13 bear-option buttons. Selecting bears updates the right-hand "→ BearName" indicator.

- [ ] **Step 2: Results tab — top-down visual check**

Switch to the Results tab. Confirm:
- "🏆 Round 1 Locked Assignments 🏆" gold-bordered section shows 9 cards (one per engineer in the locked list).
- "⭐ Round 2 Best Matches (Optimal 1-to-1 Assignment) ⭐" — shows the optimal matching for the 6 round 2 members from current votes. If no votes, shows "No votes yet!".
- "Full Voting Breakdown" — shows a row per round 2 member with vote totals for round 2 bears only.
- "Voters" chip list shows all current voters.

- [ ] **Step 3: Submit a vote end-to-end**

From the Vote tab, enter a name, pick a bear for all 6 members, hit Submit Votes. Confirm the redirect to Results tab and that your vote appears in the breakdown and updates the "Best Matches" grid.

- [ ] **Step 4: Re-verify race condition fix**

Repeat the two-window test from Task 9 Step 3 if you didn't already do it.

- [ ] **Step 5: Spec coverage check**

Open `docs/superpowers/specs/2026-04-30-care-bears-round-2-design.md` and skim each section. Confirm:
- Round 1 locked-in matches: ✓ (Tasks 3, 4, 7)
- 10 new wiki bears + Claude Bear with two-part descriptions: ✓ (Tasks 1, 2)
- Round 2 vote for Natalia, Ivan, Federico, Claude, Intern John, Megan: ✓ (Task 3)
- Round 2 bear pool of 12: ✓ (Tasks 3, 5, 6, 8)
- Race condition fix: ✓ (Task 9)
- Layout (Vote tab + Results tab) per spec: ✓ (Tasks 4, 5, 6, 7)

- [ ] **Step 6: Commit nothing (verification-only task)**

If any verification failed, file a follow-up — do NOT mark this task complete until the spec is fully satisfied.

---

## Self-review notes

Skim of spec sections vs plan tasks:
- **Round 1 locked-in assignments (spec §3):** covered by Task 3 (constant), Task 4 (Vote tab UI), Task 7 (Results tab UI).
- **Round 2 teammates (spec §4):** covered by Task 3 (`teamMembers`).
- **Round 2 bear pool (spec §4):** covered by Tasks 1 (icons), 2 (data), and the `round2Bears` filter in Task 3 + its use in Tasks 5, 6, 8.
- **Claude Bear spec (spec §4):** covered by Tasks 1 (Brain icon) and 2 (entry with #D97757 + description).
- **Page layout (spec §5):** covered by Tasks 4–8.
- **Data layer (spec §6):** `lockedAssignments` const + `round2Bears` derived array — Task 3.
- **Race condition fix (spec §7):** Task 9.
- **Manual testing (spec §8):** Task 10.

No placeholders. No "TBD". Type/property names are consistent: `lockedAssignments` shape `{ member, bear }`; `careBears` entries `{ name, icon, color, description }`; `round2Bears` is the filtered subset of `careBears`. Function references (`getVoteCounts`, `getOptimalMatching`, `handleVote`) match the existing source.
