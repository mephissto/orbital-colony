# Orbital Colony

[🇫🇷 Français](README.md) · 🇬🇧 English

A space idle game, bilingual EN / FR (English by default, French auto-detected),
installable as an app (PWA) and playable offline. The whole game fits in
`index.html`: no dependency, no server, no data ever leaving your browser.

Free software under [GPL 3.0 or later](#licence).

**Play online:** [orbital-colony.app](https://orbital-colony.app/)
· development build: [dev.orbital-colony.app](https://dev.orbital-colony.app/)

---

## Contents

- [Files and deployment](#files-and-deployment)
- [The world of the game](#the-world-of-the-game)
- [Menu and tutorial](#menu-and-tutorial)
- [Game loop](#game-loop)
- [Clicking](#clicking)
- [Structures](#structures)
- [Upgrades](#upgrades)
- [Anomalies](#anomalies)
- [The global multiplier](#the-global-multiplier)
- [Challenges](#challenges)
- [Prestige and antimatter](#prestige-and-antimatter)
- [Research](#research)
- [Automation](#automation)
- [Achievements](#achievements)
- [Offline gains](#offline-gains)
- [Saving](#saving)
- [Interface](#interface)
- [Version](#version)
- [Changing the balance](#changing-the-balance)
- [Licence](#licence)

---

## Files and deployment

| File | Role |
|---|---|
| `index.html` | the entire game — logic, styles, favicon, translations |
| `manifest.webmanifest` | name, colours and icons of the installed app |
| `sw.js` | service worker: the game runs offline |
| `icon-192.png`, `icon-512.png` | app icons |
| `icon-maskable-512.png` | adaptive Android icon (croppable to a circle) |
| `apple-touch-icon.png` | iOS icon |
| `LICENSE` | full text of the GPL 3.0 |
| `README.md` / `README.en.md` | this document, in French and English |
| `CHANGELOG.md` / `CHANGELOG.en.md` | one release note per version |
| `ROADMAP.md` | what is planned next, and why |

All files go **at the root of the repository**, flat. Installation requires
HTTPS — Cloudflare Pages, GitHub Pages and Netlify all provide it
automatically.

**Installing:** Chrome Android → ⋮ menu → "Install app"; Safari iOS → Share →
"Add to Home Screen"; on desktop the install icon appears in the address bar.

**The game offers it by itself** since 2.35.0: a dialog opens after two seconds
for players on **mobile outside app mode**, with the Android and iOS instructions
side by side. The iOS warning (⚠️ Safari required) sits on its own line — that
label carries markup, hence the `data-i18n-html` attribute that switches
`applyI18n()` from `textContent` to `innerHTML` for that element. Three
conditions to show it — coarse pointer, screen ≤ 1024 px, and
a `display-mode` other than `standalone` (plus `navigator.standalone` for Safari
iOS, which does not follow the standard). On Chrome/Android the
`beforeinstallprompt` event is captured: an **Install** button is then added and
fires the real system dialog. Elsewhere — iOS first of all, which exposes nothing
— only the instructions remain, hence both tutorials shown together rather than
one picked from the user agent, which is always unreliable.

A refusal is remembered **per device**, in a separate `localStorage` key
(`colonie_orbitale_pwa`) rather than in the save: refusing on your phone must not
follow the run exported to another machine, nor come back after an import. A
**📲 pill at the bottom right** then takes over and reopens the dialog on demand;
the toast strip reserves its width so as not to cover it.

The key has **three values**, and a single line of code separates them:

| Value | How you get there | What stays on screen |
|---|---|---|
| *(absent)* | first launch | the dialog opens |
| `no` | "Close" | the pill only |
| `never` | "Close" with the box ticked | nothing at all |
| `ok` | install accepted (`appinstalled`) | nothing at all |

The **"Don't remind me on this device"** box (2.35.2) is there for players who
want to stay in their browser: without it the pill was permanent, and the only
way to get rid of it was to install the game. It is unticked every time the
dialog opens — an already-remembered refusal must not turn into a permanent one
the first time you tap the pill.

**Updating:** the service worker always looks for `index.html` on the network
first, so a plain reload is enough after a deployment. If you change **the icons
or the manifest**, bump `CACHE` at the top of `sw.js` (`colonie-orbitale-v2` →
`-v3`) to force the cache to refresh.

---

## The world of the game

The name is not decorative: it describes the loop.

> You are not building on a world. You are building **around** one.
>
> The colony turns four hundred kilometres above a dead planet, anchored to an
> asteroid captured into the same orbit. Nothing lands, nothing leaves:
> everything you build stays suspended there, between the void and the gravity
> well.
>
> And the orbit decays. Always. Every colony falls back in the end, and all that
> survives is a handful of antimatter torn from its own descent — enough to
> build the next one a little higher, a little faster.
>
> That is where the name comes from: **an orbital colony is never finished, it
> is only falling more slowly than the last one.**

This text is the `lore` key of the `T` dictionary, in French and English. It
opens the tutorial. It also justifies
prestige: restarting a cycle is not a genre convention, it is the orbit giving
way.

---

## Menu and tutorial

Up to 2.35 the top bar carried three buttons — Save, Export / Import, Reset.
3.0.28 groups them behind **a single Menu button**: at five entries the bar no
longer fitted on a phone, and Reset sat dangerously next to Save.

The menu holds a what's-new summary (the `wn_d` key, three lines, to be
rewritten at every notable version) then **five entries as a list**, one per row
and full width: Save, Export / Import, Reset, Tutorial, Changelog. Each carries a
**subtitle** saying what it does — "the game already does it on its own, every
20 s" under Save answers the only question that button raises. Subtitles have a
short variant (`_s`) on narrow screens.

Below them, a **☕ Support the game** button (to Ko-fi) and a **footer**: author and version
on one line, source code and licence underneath.

The two-column grid lasted from 3.0.0 to 3.1.1. It was dropped for three reasons:
the touch target was only half the width, there was no room to explain an entry,
and **the About window held only four lines** — they fit in the footer, which
removes a whole window.

**Window stacking order**, to respect if another one is added:

| Window | z-index |
|---|---|
| Menu | 82 |
| Export / Import, Changelog | 84 |
| Tutorial | 86 |
| Install prompt (PWA) | 90 |
| Confirmation (`demande()`) | 95 |

Export opens **from** the menu: at 80 it opened behind it, visible but unusable,
with the menu intercepting clicks.

**Closing on an outside click** requires both the press **and** the release to
happen on the backdrop. A click alone is not enough: on touch the browser
synthesises a `click` after `pointerup` and places it on whatever is under the
finger then — that is, on the backdrop of the window that just opened, which
closed again immediately (fixed in 3.1.1). The `demande()` confirmation is
excluded from this: an irreversible action is not cancelled by a stray click.

**Changelog** — the `LOG` table embeds the latest versions summarised in one
sentence, bilingual, with a link to the repository's full `CHANGELOG.md`. The
full file is 37 KB per language and the game has to stay playable offline: the
excerpt is the accepted trade-off, to be kept up to date at every release.

**Tutorial** — five screens (`TUTO`): the lore, the asteroid, structures,
upgrades and anomalies, the cycle. It opens **by itself on the first run** and
can be replayed from the menu. Arrows on either side of the dots, and swiping in
both directions.

Two delicate points:

- It comes **before** the PWA install prompt. `tutoFerme()` calls `majPwa()`
  only if the tutorial had opened automatically — two stacked dialogs on the
  first launch is one too many.
- A run already in progress does not trigger it. The `S.tuto` field does not
  exist in pre-3.0.0 saves and reads 0 there, so `partieVierge()` also looks at
  `totalOre`, `prestiges` and `clicks`, and the flag is set immediately so it
  never comes back.

The outbound links are the `LIEN_CODE`, `LIEN_AUTEUR`, `LIEN_KOFI` and
`LIEN_MARQUE` constants, at the top of the block, and carry
`rel="noopener noreferrer"`.

---

## Game loop

You mine **ore**, the only running resource. Ore buys **structures** that produce
it automatically, and **upgrades** that multiply that output. When progress slows
down, you start a new **cycle**: you lose everything but gain **antimatter**,
which permanently raises your output and funds permanent **research**.

```
   click ─┐
          ├─→ ORE ─→ structures ─→ output /s ─┐
 offline ─┘    └─→ upgrades ────────────────┤
                                             ├─→ ×  global multiplier
   anomalies ─→ temporary buffs ────────────┤
   achievements ─────────────────────────── ┤
   antimatter + research ────────────────────┘
                    ↑
                 prestige (resets the cycle)
```

---

## Clicking

Clicking the planet yields ore immediately. A click's value is the sum of two
terms, the whole multiplied by the global multiplier:

```
click = ( strike + echo ) × click upgrades × Servo arms × active click buff

  strike = 1 × global multiplier
  echo   = output/s × max( best resonator owned , base echo 10 % )
```

- **Base echo: 10 %** (7 % in 3.0.28, raised in 3.3.0). Without it the echo only exists once the first
  resonator is bought (200,000 ore), and the strike — a base of 1 that never
  grows — drops off immediately: measured on a fresh run, the click stayed at
  1–1.5 while output reached 246/s, i.e. **0.006 s of output per click** after an
  hour. With the base echo the click is worth **~0.10 s of output** permanently,
  from the first minute. It is applied **inside** the echo, not as a floor on the
  result: a floor on the final value would hide the effect of click upgrades —
  exactly the defect fixed in 2.30.0.
- **Click upgrades** — **on the click's total value**, echo included: Ion hammer
  ×1.45, Exoskeleton ×1.52, Capacitor ×1.58, Magnetic field ×1.64. In full:
  **×5.71**. Each is worth exactly its ×N however far along you are, which is
  what the card states as a single number.
- **Servo-assisted arms** (research) — **+8 % per level on the whole click**,
  12 levels, i.e. **×2.52** at maximum. Up to 2.32.0 it was ×2 per level **on the
  strike alone**: measured, an advanced player restarting a cycle ended up with
  exactly the same ore and the same output after 5 minutes with 0 or 12 levels —
  8,675 antimatter for nothing. The price ladder has not moved by a single
  antimatter, which keeps the research consistent with the other seven (whose
  first levels all cost between 6 and 30).
- **Resonators** — replace the base echo with a higher percentage of your output
  per second: v1 +12 %, v2 +13.5 %, v3 +15 %. Only the best one counts, they do not
  stack.

**The balance rule: a click must never exceed output/s.** The click's ceiling is
the product **best echo × click multipliers**. With resonator v3 at 40 % (up to
2.30.0) the click was already worth 0.40 s of output **before any click upgrade**
— so no multiplier above ×2.5 could be added without breaking the rule. That is
why the resonators dropped to 2/5/10 % at the same time as the multipliers moved
to ×1.5–×2.

3.0.28 replays that same trade-off in the other direction: **the multipliers drop
(×5.71 in full) so the echo can go up**. Click power thus moves towards the
**early** game, where it was missing, without changing the ceiling at all:
0.15 × 5.71 = **0.86 s of output**, exactly the previous value.

3.3.0 pushes that same slider one notch further, still at a constant ceiling.
Three constraints govern the setting:

1. **The echo can never exceed the first resonator**, since the game takes the
   maximum of the two. Raising the echo to 10 % without touching v1 (9 %) would
   have made v1 *strictly useless*, which is why the resonators move to
   12/13.5/15 %.
2. **v3 sets the ceiling** and therefore stays at 15 %.
3. **At a constant total, raising the first upgrade forces the last one down.**
   The geometric mean of the four is 1.546: that is the highest the Ion hammer
   can go without becoming stronger than the Magnetic field. Hence the tightening
   to 1.45/1.52/1.58/1.64.

The fourth lever carries no such constraint: **lowering the costs**. The Ion
hammer goes from 400 to 250, the Exoskeleton from 35,000 to 18,000. The player
reaches the same multipliers earlier, which moves the curve without touching a
single balance value.

Measured result, in seconds of output per click:

| Stage | 3.2.3 | 3.3.0 |
|---|---|---|
| start, no upgrade | 0.070 | **0.100** |
| Ion hammer | 0.102 | **0.145** |
| + Resonator v1 | 0.131 | **0.174** |
| + Exoskeleton | 0.199 | **0.265** |
| + Capacitor, v2 | 0.382 | **0.470** |
| + Magnetic field, v2 | 0.650 | **0.771** |
| + Resonator v3 | 0.857 | 0.857 |
| servo 12/12 | 2.158 | 2.157 |

**The only thing allowed to cross that ceiling is the Servo arms research** — and
only once all twelve levels are paid for: at 12/12 the click reaches **2.16 s of
output**. That overshoot is deliberate: a player who sank 8,675 antimatter into a
research is entitled to see its effect.

| Stage | Echo | Click upgrades | Servo | sec. of output/click |
|---|---|---|---|---|
| First minutes | 10 % base | 0 | 0/12 | 0.10 |
| Exoskeleton | 10 % base | 2 | 1/12 | 0.24 |
| Resonator v2 | 13.5 % | 3 | 7/12 | 0.75 |
| Magnetic field | 13.5 % | 4 | 9/12 | 1.54 |
| Resonator v3 | 15 % | 4 | 11/12 | 2.00 |
| Very advanced run | 15 % | 4 | 12/12 | **2.16** |

The dip at the second stage is expected: the first two click upgrades
land before the first resonator, and that is the one moment where the strike (the
constant term) has already dropped off while the echo has not yet grown. Before
3.0.28 that dip was 0.01 s.

To watch if these values change: the **Mining satellites** click up to 10 times a
second. At 2.16 s of output per click they therefore yield **21.6× passive
output** — automatic clicking remains, as before, the main ore source of an
advanced cycle.

**History of this formula.** Up to 2.28.0 the click upgrades only multiplied the
strike — a base of 1 that never grows — while the echo follows your output.
Measured: the Magnetic field ×8 was worth ×7.33 early on but **×1.00** with
resonator v3 and a large output, for 2 billion ore. 2.29.0 tried a double effect
(×N on the strike + echo points): correct on paper, but unreadable on a card.
2.30.0 settles it with **a single number applied to everything**.

---

## Structures

Ten structures, each producing ore continuously.

| # | Structure | Base price | Base output |
|---|---|---|---|
| 1 | Mining drone | 15 | 0.1 /s |
| 2 | Automated drill | 120 | 1 /s |
| 3 | Laser extractor | 1,400 | 8 /s |
| 4 | Orbital refinery | 20,000 | 52 /s |
| 5 | Nanite swarm | 240,000 | 300 /s |
| 6 | Space elevator | 3,000,000 | 1,800 /s |
| 7 | Asteroid crusher | 45,000,000 | 11,000 /s |
| 8 | Stellar forge | 800,000,000 | 75,000 /s |
| 9 | Dimensional ripper | 1.5e10 | 540,000 /s |
| 10 | Dyson sphere | 3e11 | 4,200,000 /s |

**Price of the n-th unit:** `base price × 1.15^(already owned)`, reduced by the
Negotiation research. The **MAX** button works out how many you can buy at once,
geometric sums included.

The **×1 / ×10 / ×100 / MAX** row is **sticky**: it stays at the top of the tab
while you scroll the list (3.0.28). It decides the price and quantity shown on
*every* card, and with ten structures you had to scroll back up every time you
changed your mind. Its anchor point is computed in JS because it differs per
layout: on desktop `#panels` is the scroller and the row sticks to its top edge
minus its padding, on mobile it is `<main>` and the row lands under the sticky
tab header (the `--buyTop` CSS variable, updated by `syncHero()`).

**Total output:**

```
output/s = Σ ( count × base output × that structure's tiers )
           × global multiplier
```

A structure only appears in the list once you have come close to its price
(35 %), and the next two show as "???". A discovered structure stays discovered,
even after a prestige.

---

## Upgrades

**One-off, permanent** purchases, paid in ore, lost on prestige. They appear in
the list as soon as you have mined 8 % of their price **during the current cycle**
(`S.runOre`). The criterion stays monotone within a cycle — the list never jumps
around while you play — but it resets at prestige, like the upgrades themselves:
every cycle rediscovers its list as you mine. Up to 2.28.0 it was based on the
lifetime total, never reset: from the second cycle on, the whole list appeared at
once.

**Structure tiers** — 6 per structure, 60 in total. Each requires a number of
units and multiplies that one structure's output:

| Tier | Units required | Effect | Price |
|---|---|---|---|
| 1 | 10 | ×2 | base price × 18 |
| 2 | 25 | ×2 | × 11 |
| 3 | 50 | ×3 | × 11 |
| 4 | 100 | ×4 | × 11 |
| 5 | 175 | ×5 | × 11 |
| 6 | 250 | ×6 | × 11 |

A fully upgraded structure produces **×1,440**.

**Click upgrades** — on the whole click: Ion hammer ×1.45 (250), Exoskeleton
×1.52 (18,000, from 50 clicks), Capacitor ×1.58 (5e6, from 250 clicks), Magnetic
field ×1.64 (2e9, from 600 clicks). In full: **×5.71**.

**Resonators** — v1 +12 % (2e5), v2 +13.5 % (4e8, requires v1), v3 +15 % (6e11,
requires v2). Below them, the base echo is already worth **7 %**.

**Global upgrades** — Logistics network ×1.25 (5e4), Coordination AI ×1.5 (8e6),
Quantum relay ×2 (1.2e9), Bio-engineering ×2.5 (3e11), Singularity engine ×4
(9e13). In full: **×37.5**.

**Anomaly beacon** (1e7) — anomalies appear 30 % more often.

Total: **73 upgrades**.

**How owned upgrades are sorted** — the list of already-purchased upgrades is
split into sub-sections: one per structure for the tiers (in Mining-tab order),
then Click power, Click resonance, Global output and Anomalies. Each header
carries an `owned/total` counter that turns gold once the family is complete. A
category only shows up once you own your first upgrade in it: the counter tells
you *how many* are left in the family, never which ones, so nothing is revealed
early.

---

## Anomalies

An anomaly appears regularly somewhere on screen. It stays for **14 seconds**,
then vanishes. Clicking it triggers an effect and restarts the countdown.

**Frequency:** a random interval between **110 and 240 seconds**, reduced by the
Anomaly beacon (×0.7) and by the Detector research (×0.8 per level). At the
maximum of both: between 25 and 55 seconds.

**Possible effects:**

| Anomaly | Chance | Effect |
|---|---|---|
| 🌟 **Time leap** | **1 %** | **20 to 30 minutes** of output, at once |
| ⚡ Power surge | 5 % | output **×5 to ×10** for 45 s |
| ✨ Quantum echo | 5 % | click **×6 to ×12** for 60 s |
| 💎 Rich vein | 44.5 % | **120 to 300 s** of output, at once |
| 📦 Abandoned cache | 44.5 % | **+15 to 20 %** of your ore in reserve |

**Every anomaly rolls its value at random** within the range shown, on every
appearance — the message and the badge display the exact amount obtained
("Output ×6.4", "+813K ore (21 minutes ahead)"). Multipliers are rounded to one
decimal.

Multipliers are deliberately rare, and **only one buff can be active at a time**,
output and click alike: a new one replaces the previous. They stacked until
2.21.0 — four ×10 power surges caught back to back gave **×10,000 on output**,
and a click buff on top pushed it to ×490,000. The vein and the cache, by
contrast, stay proportional to your progress. Durations are extended by 30 % per
Detector level.

The rich vein and the time leap compute their gain from your **base** output,
ignoring any power surge currently active: otherwise, catching a vein or a leap
right after a ×10 surge would have multiplied their gain by 10, contradicting
the rule below ("never grants power you do not already have, only time ahead").
Fixed in 2.21.2.

The click buff applies **only to the player's own clicks**, not to those of the
Mining satellites. Otherwise the Quantum echo stopped being a reward for an
attentive player and became a disguised output multiplier: with ten satellites
and the resonator maxed out, a click is worth 0.4 × your output, so a ×12 on
automatic clicks was worth ×49 on total output. By hand, at five clicks per
second, a ×12 still yields the equivalent of 24 times your output — the reward
stays strong, but you have to be at the screen.

### The time leap

This is the jackpot: a jump of **20 to 30 minutes forward**, credited instantly.
That is **four to fifteen times** a Rich vein, while staying proportional to your
progress — it never grants power you do not already have, only time ahead. That
is deliberate: it speeds up the run without shortening the progression curve.

Visually you cannot miss it: bigger (96 px against 62), an iridescent star from
white to cyan to violet, a bluish halo pulsing twice as fast, and above all
**arcs spinning four times faster** than on an ordinary anomaly — the image of
time running wild.

**Approach signal:** a countdown is shown above the planet. Under **10 seconds**
it turns magenta, and so does the planet's ring. While the anomaly is on screen
it reads "In sight!".

---

## The global multiplier

It multiplies **all** of your output, clicking included. It is the product of
four families:

```
multiplier = (1 + antimatter × bonus per unit)   ← antimatter
           × (1 + 0.01 × achievements earned)    ← achievements
           × 1.3^(Mining optimisation)           ← research
           × global upgrades                     ← ×1.25 … ×4
           × active output buff                  ← anomalies
```

The full breakdown is available as a tooltip on the Multiplier tile.

---

## Challenges

Six special runs, opened when the header tile reads **"cycle #6 in progress"**.
Each breaks **one** rule of the game for a whole cycle.

Entering **banks the current cycle first** and credits the antimatter you had
pending, then resets ore, structures and upgrades. Kept: antimatter, research,
achievements, automations and the rewards of challenges already cleared. A
permanent banner shows progress, with a **Leave** button and no penalty. Once
beaten, a challenge is cleared **for good**.

During a challenge, **only the ♻️ cycle restarter is suspended** — it would wipe
your progress. Every other automation keeps working. Research cannot be bought;
the tab is greyed out and says why.

| Challenge | Broken rule | Permanent reward | coef |
|---|---|---|---|
| 📵 Radio silence | no anomaly appears | anomalies 20 % more frequent | 0.005 |
| ⛓️ Hands tied | clicking yields nothing | output +25 % | 0.002 |
| 📈 Inflation | prices ×1.35 instead of ×1.15 | −8 % on every price | 0.006 |
| 🏚️ Dwarf colony | only 6 structures, but they produce ×4 | the last 4 produce ×2 | 0.0015 |
| 💨 Containment leak | output −2 % every 2 min, floor at 20 % | antimatter gain +15 % | 0.015 |
| 🕳️ The Void | antimatter no longer counts in the multiplier | antimatter exponent 1.50 → 1.55 | 0.005 |

**The goal** is `coef × best cycle × handicap`, in ore mined during the cycle,
and is **frozen when you enter**. It cannot be a hard-coded number: measured, two
players at 10 and 24 cycles took 78 min and 1 min for the same amount of ore.

**The handicap** answers "how much does this rule slow down THIS player". It is 1
for five challenges out of six and is only computed for **The Void**, whose
penalty depends entirely on progression: a player with 2 antimatter loses ×1.2, a
player with 500,000 loses ×40,000,000. It is measured on the colony **still
standing**, right before the reset.

**Balance was measured, not estimated**: second-by-second simulation of a real
save, anomalies, buffs and automations included, each challenge compared to an
ordinary cycle aiming at the same goal. Between 2.4 and 3.8 h for an attentive
player, or 2.5 to 5 times a normal cycle. The full table, and the two challenges
that were *arithmetically impossible* before that measurement, are in
[ROADMAP.md](ROADMAP.md).

Two lessons from that work, worth keeping if a challenge is ever added:

- For a **constant-factor** rule (no anomalies, no clicking, higher prices), the
  coefficient only sets the **duration** — the difficulty ratio does not depend
  on it.
- For a rule that **compounds over time** (the leak, the crippled colony), the
  ratio explodes with the goal: it is tuned by **bounding the rule**, not by the
  coefficient. Without a floor, the leak bounded the cycle's total ore to
  `output × 1485 s` — the challenge was not hard, it was over before it began.

---

## Prestige and antimatter

Starting a new **cycle** resets: ore, structures, upgrades, running buffs. You
**keep**: antimatter, research, achievements, already discovered structures,
statistics.

**Antimatter earned:**

```
gain = ⌊ ( ore mined during this cycle ÷ 1e10 ) ^ 0.30 ⌋
```

So it takes **10 billion** ore mined in the cycle for the first unit
(`AM_SEUIL`), and the exponent `AM_EXPG` is **0.30**. Returns fall off fast:
101 billion for 2 units, 21.5 T for 10, 46.4 Qa for 100, 100 Qi for 1,000.

The exponent went from 0.32 to 0.30 in **2.33.0**. The click fixes (2.30 → 2.32)
had, without that being the goal, nearly doubled the ore of an advanced cycle —
and therefore the antimatter gain, up from 36.2K to 69.9K per cycle. But
antimatter has a single outlet, the 8 researches (234,890 in total): doubling the
gain meant halving the time before it becomes useless. The exponent is the right
lever rather than the threshold, because it **leaves the first prestige
untouched** (1 antimatter either way) and corrects all the more strongly as the
cycle grows — exactly where the inflation happened.

| Cycle | Ore | Gain at 0.32 | Gain at 0.30 |
|---|---|---|---|
| 1st prestige | 10.0B | 1 | 1 |
| Mid-game | 10.0Qa | 83 | 63 |
| Advanced | 100Qi | 1.58K | 999 |
| Very advanced | 13.8Sp | 69.9K | 34.8K |

Maxing the 8 researches therefore goes from **4 cycles to 7** for a very advanced
player, and a fully equipped player is back to the pre-2.30.0 yield (×0.96).

### Why this exponent, and not a square root

Until 2.19.0 the gain was `12 × √(ore / 1e12)`. Measured in simulation —
automatic rebuying of structures and upgrades, restart threshold at +50 % — a
cycle then lasted **about twenty seconds, at 100 as at 1,000,000 antimatter**.
Antimatter was effectively free, and no research price could do anything about
it: at that rate, any price scale is exhausted in minutes.

The cause is not the threshold but the exponent. A cycle's length is set by
**rebuying the structures**, not by the antimatter threshold: once the structures
are rebuilt, output is such that the threshold falls instantly, whatever it is.
Multiplying the threshold by 16 only took a cycle from 21 to 40 seconds. What was
needed was for the required ore to grow **faster than output**, hence an exponent
well below 0.5.

Length of a cycle yielding +50 % antimatter, with research levelled in parallel:

| Antimatter | Before (square root) | After (fractional exponent) |
|---|---|---|
| 200 | 21 s | 25 min |
| 1,000 | 18 s | 9 min |
| 5,000 | 19 s | 13 min |
| 20,000 | 20 s | 47 min |
| 100,000 | 22 s | 1.5 h |
| 1,000,000 | 26 s | 18.8 h |

The shortest cycles now sit around 1,000 to 5,000 antimatter — the heart of the
game — then lengthen progressively. The threshold for the first unit was
**lowered** at the same time (10 billion instead of the original 6.94, then 20 in
2.19.0): it was the top of the curve that needed stretching, not the early game.

**Permanent bonus:** each unit of antimatter grants **+2 %** output. The
Resonance research adds +1.5 points per level, i.e. **+17 % per unit** at level
10 — by far the biggest lever in the game.

The total is then **raised to the power 1.5**:

```
multiplier = ( 1 + antimatter × bonus ) ^ 1.5
```

Without that exponent the multiplier rose *linearly* with antimatter while
structure prices rise *exponentially* (×1.15 per purchase): every cycle therefore
paid mechanically less than the previous one. With the power 1.5, the multiplier
grows fast enough to offset the ore required, and a cycle keeps a fairly stable
length very far into the game. The exponent is the constant `AM_EXP`.

| Antimatter | Old bonus | Current bonus |
|---|---|---|
| 10 | ×2.7 | ×4.4 |
| 100 | ×18 | ×76 |
| 1,000 | ×171 | ×2,236 |
| 10,000 | ×1,701 | ×70,155 |

*(values at Resonance level 10)*

---

## Research

Paid in **antimatter**, never lost. The price of level `n` is
`base cost × growth^n`.

| Research | Effect per level | Max | Base | Growth | Last level | Total |
|---|---|---|---|---|---|---|
| ⚙️ Mining optimisation | +30 % output | 15 | 8 | ×1.8 | 29,986 | 67,463 |
| 🤖 Servo-assisted arms | ×2 click power | 12 | 6 | ×1.8 | 3,857 | 8,675 |
| 💾 Buffer memory | +3 h of offline gains | 8 | 10 | ×2 | 1,280 | 2,550 |
| 🔁 Automation | +10 % offline efficiency | 6 | 14 | ×2.1 | 572 | 1,081 |
| 💠 Negotiation | −4 % on structure costs | 10 | 16 | ×2 | 8,192 | 16,368 |
| 📶 Anomaly detector | anomalies +25 % more frequent, +30 % longer | 5 | 20 | ×2.2 | 469 | 843 |
| ✨ Antimatter resonance | +1.5 % bonus per antimatter | 10 | 30 | ×2.4 | 79,255 | 135,847 |
| 📦 Starter capsule | ore granted at every new cycle | 6 | 22 | ×2.2 | 1,134 | 2,063 |

Completing everything costs **234,890 antimatter**, of which 135,847 for
Resonance alone. Growth never drops below ×1.8: each level must visibly cost more
than the previous one **from the start**. The old scale began at 4 antimatter
with ×1.55 growth, giving 4 → 7 → 10: the progression was there, but invisible to
the eye on such small numbers.

The Capsule grants `10,000 × 25^level` ore at the start of each cycle, i.e.
2,441 billion (2.44e12) at level 6. That ore is **granted, not mined**: it does
not count towards the cycle total and earns no antimatter. Before 2.21.0 it did
count, and at level 6 every cycle started with 5 antimatter already earned
without playing a second.

---

## Automation

The **Auto** tab, revealed at the first prestige cycle. Like research,
automations are paid in **antimatter** and are never lost. They buy comfort, not
power: the Mining satellites do nothing an attentive player could not do by hand.

**The mining satellites** — as soon as they run, **one dot per level** goes into
orbit around the planet, evenly spread around the circle: you read your level by
counting them. **Both rates are fixed**, one turn in 8 s and a 2.4 s pulse per
dot, whatever the level — it is the number of dots that carries the information,
speed has nothing to add. The pulses are simply offset from one dot to the next,
so that a wave travels around the ring exactly once per rotation. Their card in
the Automation tab does not blink: it already spells out "N satellites in orbit",
and one more animated dot added nothing but a distraction in a panel you come to
read, not to watch.

The orbit radius is calibrated so the dots never spill over anything: on mobile,
the usable space is the gap between the bottom of the anomaly chip and the top of
the tab bar, and on desktop the planet additionally reserves a vertical margin.

As for cost, a single element is animated — the whole ring — and only through
`transform:rotate`, the property the compositor handles on the GPU without
recomputing layout or repainting. The DOM is only rebuilt when the level changes
(`majOrbite()` returns immediately if the count has not moved): in steady state
there is not one line of JavaScript per frame. An animation's period does not
change its cost either — it is played at the screen's refresh rate whatever its
duration — so level 10 is no heavier than level 1 in that respect.

The orbit disappears if the automation is switched off, and the animation is
disabled if the system asks for reduced motion (`prefers-reduced-motion`).

Deliberately, no floating "+N" is emitted for an automatic click: at 10 clicks/s
it would be unreadable, and it would drown the "+N" of manual clicks, which are
the visual feedback of the player's own gesture.

**Every automation has a switch**, in the Settings section at the top of the
tab. Switching it off refunds nothing and loses no level: just switch it back
on.

| Automation | Effect | Max | Cost | Cumulative cost |
|---|---|---|---|---|
| 🛰️ Mining satellites | +1 click/s per level | 10 | 30, ×2 per level | 30,690 |
| ⬆️ Engineer | buys the cheapest affordable upgrade | 1 | 100 | 100 |
| 🏗️ Foreman | buys the structure you assign it, every second | 1 | 150 | 150 |
| 📡 Recovery probe | collects the anomaly for you | 1 | 200 | 200 |
| ♻️ Auto cycle | starts a new cycle at the chosen threshold | 1 | 400 | 400 |

**Why the Foreman costs more than the Engineer** (150 against 100, swapped in
2.34.0): the two are mutually exclusive, and the one you actually keep switched
on is the Foreman, which buys structures continuously. The Engineer's job, by
contrast, is **finished** once all 73 upgrades are bought — past that point it
has nothing left to do. The more useful one therefore had to be the more
expensive. The automation total is unchanged: this is a swap, not a rise.

Automating everything costs **31,540 antimatter**, against 234,890 to finish
research. Those prices were divided by 3.3 in 2.20.0: they had been set when
antimatter accumulated fast, and the change to the gain formula had put them out
of reach.

The Mining satellites alone account for **30,690**. Their last four levels
(1,920, 3,840, 7,680, 15,360) remain a goal long after everything else is bought.

Only the Mining satellites have several levels, because their number *is* their
effect. The others have a single tier: they do one thing, they do it well, and
splitting them into levels would only have spread a purchase artificially.
Foreman: one purchase per second (`CONTRE_S`). Probe: collection 2 s after the
anomaly appears (`SONDE_S`), well under an anomaly's 14 s lifetime.

**At the top of the tab**, a **Settings** section groups the switches and the two
settings, each revealed by the automation it concerns. Each box contains, in this
order: the label and its control on the same line, then a rule, then the
**concrete figure** of the moment, then the explanation in small type. Settings
come **before** the automation list: once those are bought, the list only serves
for buying and levels, whereas the panel is what you come back to.

**Active automations** — one row per owned automation: icon, name, current state
and its switch. Switching one off refunds nothing and loses no level. Under
520 px wide the state moves **below** the name instead of disappearing: it is the
one carrying "paused by…", the most useful information on the row.

**The Foreman buys** — a dropdown listing the structures **already revealed**
(`genRev()`, bounded by `S.seen`: nothing is disclosed early), with their icon.
It only ever buys that one, one per second. Until the player picks one,
`autoGenId()` aims at the **last revealed structure**; as soon as they pick,
`S.autoGen` is written and no longer moves on its own. The line shown gives the
target price, the ore still to own and a time estimate.

**The Foreman and the Engineer are mutually exclusive.** They draw on the same
ore: switching one on **pauses** the other, the switch shows it, and the player
decides which one works. **Only one of the two can run at a time, and the other's
switch is inert** (`verrouille()`, `not-allowed` cursor): to hand control back to
the Foreman you must switch the Engineer off first. One extra click, but you can
never believe you switched an automation back on when it will not actually run.

An automation therefore has **three states**, and telling them apart is what
makes the whole thing work:

| State | Field | Cleared |
|---|---|---|
| active | — | — |
| **switched off by hand** | `S.autoOff` | never automatically: it is the player's intent |
| **paused** by its exclusive partner | `S.autoPause` | as soon as the other stops, for any reason |

`S.autoMain` remembers which of the two took over last, `normExclus()` recomputes
the pauses from the player's intents alone (idempotent, called after every
change, on load and on import), and `buyAuto()` hands control to the one you just
paid for. Concretely: switching the Engineer off **hands control back to the
Foreman** if it had only been suspended, but does not revive a Foreman the player
deliberately switched off. `migrerExclus()` reads back a save from before 2.26.0,
where the pause was written as a manual switch-off. A row
paused by exclusivity reads "paused by *the other automation*" rather than
"switched off" — `enPause()` returns the automation responsible, `txtPause()`
names it via its `nmd` field (name with article) — and **its switch keeps the
knob on the right, merely greyed out**: the automation
is armed, the game suspended it — telling that apart from one you switched off
yourself avoids thinking you turned it off by mistake. Both cards say so in their
description too ("pauses the Engineer" / "pauses the Foreman").

Two **automatic** arbitration rules were tried then dropped, because neither was
legible while playing:

| Rule | Structures | Upgrades | Final output |
|---|---|---|---|
| none (Drone target) | 33 | 13 | 37,515 /s |
| half the stock to the Foreman (2.24.0) | 31 | 15 | 56,267 /s |
| reserve the next upgrade's exact price | 0 | 15 | 56,198 /s |

Measured over 30 simulated minutes in mid-game. Reserving the exact price pinned
the Foreman at zero purchases: the stock never durably exceeds the next upgrade's
price, since the Engineer buys it the moment it is reached. Half the stock gave
better figures, but the player saw structures slow down without understanding
why — an invisible arbitration is worth less than an explicit switch. The %
spending cap (`S.autoPart`), which played that role up to 2.23.0, disappeared
from the UI in 2.24.0; the field stays in the state so earlier saves and exports
remain symmetric.

**Restart the cycle from** — a **threshold typed by hand**, in antimatter
(`S.autoCyc`, 50 by default). `cycSeuil()` applies a **floor at 10 % of the
antimatter you own** (`CYC_MIN`): a fixed number does not follow progression, and
set to 50 then forgotten it triggered a cycle per frame once the stock reached
100,000 — measured at **600 cycles and +75,400 antimatter in one minute**. The
panel always shows the threshold actually applied, never just the typed value.
The Auto cycle restarts as soon as `amGain()` reaches that number. An absolute
threshold is self-explanatory but does not follow progression: it is up to the
player to raise it, and the line under the field gives their current gain, what
is left to reach and a time estimate to help them choose. The Auto cycle card
repeats the same threshold.

The field is never rewritten while typing (`document.activeElement`), and an
empty or zero value is ignored: the last valid threshold is restored on leaving
the field.

Older saves may hold a cap that no longer exists (25 %): `normPart()` silently
brings it back to the nearest step.

**Safety net** — `runAutos()` caps the time it processes at one second. Coming
back from the background or from offline therefore never triggers thousands of
clicks at once: automation does not play during your absence, only the usual
offline gains apply.

---

## Achievements

**78 achievements**, each granting **+1 % output** — so **+78 %** in full. They
are **never lost** on prestige.

The tab sorts them into **nine categories** (the `ACHCATS` array, whose order is
the display order; each achievement's `c` field says which section it belongs
to). Each section heading shows its progress, and turns gold once the category is
complete.

| Category | Count | What it measures |
|---|---|---|
| 🖱️ Clicking | 12 | number of clicks up to 1,000,000, click power from 1 M to 1 Sx |
| 🏗️ Structures | 12 | drones, Dyson spheres, "X of each" tiers, totals |
| ⛏️ Mining | 8 | ore mined, from 1 M to 1 Oc |
| ⚡ Output | 5 | output per second, from 1 K/s to 1 Qa/s |
| 🔬 Upgrades and research | 4 | upgrade purchases, completing both trees |
| ✦ Anomalies | 14 | anomalies caught, in total and by type |
| ♻️ Cycles and antimatter | 8 | number of cycles, antimatter held |
| ⚙️ Automation | 8 | buying and using the automations |
| 🎯 Challenges | 7 | one per challenge beaten, plus one for all six |

The two ladders in the Clicking category are deliberately separate: the **number**
of clicks (which the Mining satellites push up by 10/s, so 1,000,000 in about
thirty hours) and the **power** of a click. The power ceiling was raised to 1 Sx
because 1 Qa is crossed around "100 of each structure + all upgrades + 100
antimatter", so well before the end of the game; at 250 of each and 100,000
antimatter you go past 1 Sx.

Two achievements in the Anomalies category are not counters: **Perfect
resonance** asks you to catch a buff while another is still running (it required
two simultaneous buffs before 2.21.0, which became impossible), and **Lightning
reflex** an anomaly collected **by hand** in under 2 s. Since the Probe waits
exactly 2 s, that one can only be earned by actually being at the screen — it is
the only achievement in the game that asks for dexterity.

The thresholds **per anomaly type** are matched to the draw probabilities: at 500
anomalies caught you have on average 223 veins, 223 caches, 25 surges, 25 echoes
and 5 time leaps. The five thresholds (200 / 200 / 25 / 25 / 5) therefore unlock
at roughly the same moment as "Eye of the void", which asks for 500 anomalies.
Counting by type uses the `k` key of each `ANOMS` entry and the `S.anomK` counter.

The eight automation achievements run from the first purchase (Delegation) to the
Mining satellites at level 10, and to every automation maxed out (Self-running
colony). Two of them are about **use** rather than purchase: 50 anomalies
collected by the Probe (`S.asonde`) and 10 then 100 cycles restarted by the Auto
cycle (`S.acyc`) — two counters added to the save, with no effect on older saves,
which simply start from zero.

---

## Offline gains

Time spent away from the game is credited on your return, capped and at reduced
efficiency:

```
gain = output/s × min(absence, cap) × efficiency
```

- **Cap:** 4 h base, +3 h per Buffer memory level → **28 h**.
- **Efficiency:** 35 % base, +10 points per Automation level → **95 %**.

An absence of **less than 90 seconds** — switching tabs, a locked screen for a
moment — is paid **in full, uncapped**. Beyond that, the rules above apply and a
message announces the gain on your return.

A temporary buff that expired during the absence is not counted, and a system
clock moving backwards credits nothing.

---

## Saving

The game is saved automatically **every 20 seconds**, as well as whenever the tab
closes, in the browser's `localStorage` — so it is tied to the domain and to the
device.

The **Export / Import** button produces a text code containing the whole run:
that is the only way to move it from one device to another, or to recover it if
you change hosting. If storage is unavailable (strict private browsing), the game
falls back to memory only and says so.

---

## Interface

**Desktop** — fixed left column (planet, tiles, badges), tabs and lists on the
right. The column becomes scrollable if the window is too short.

**Mobile** — sticky header at the top (planet on the right, 2×2 tiles on the
left, badges underneath), lists below. A **horizontal swipe** in the content area
moves from one tab to the next.

**Tab bar** — an inactive tab keeps its tab shape, muted: same background and
same border as the active one, simply much more discreet, with its icon
desaturated. The current tab stands out through its solid background and a cyan
edge along its top. Previously only the active tab had a shape, the others
floated as free text and you could not see there was a bar at all.

Each tab carries an icon and its **full** label, at every screen size: no more
abbreviations like "Upgr." or "Stats". When the whole thing does not fit — which
is the case from 430 px, where the six tabs demand 772 px — the bar **scrolls
horizontally**, and a fade appears on the side where tabs remain to be seen
(`fl` / `fr` classes set by `majNavFade()`). The mask is applied to the scrolling
container itself, so it does not move with the content. Changing tabs adjusts
the view by the **minimum** needed to reveal the chosen tab — never more —
including by swiping. Two ◀▶ arrows appear over the fade to scroll with a mouse or trackpad; they
only show on the side where tabs are actually hidden, and only when there's
no touchscreen (`pointer:fine`) — a narrow window on a computer benefits from
them just as much as a large screen.

Two touch precautions: the bar carries `touch-action:pan-x`, so only a
left-right slide reaches it — without which the slightest vertical component of
the gesture scrolled the page at the same time and the bar seemed to move up and
down under the finger. And recentring writes `scrollLeft` by hand rather than
calling `scrollIntoView`, which scrolls *every* scrollable ancestor: under a fixed
header, the browser believes the tab is hidden and causes a vertical jump.

**Touch** — a purchase is only confirmed when the finger is lifted, and only if
you have not moved more than 12 px in less than 0.9 s: scrolling never triggers a
purchase by mistake. Clicking the planet stays instant.

**Double-tap zoom** — neutralised by three successive barriers, because none is
reliable everywhere: `touch-action:manipulation` set explicitly on every
scrolling container, cancelling the second nearby tap (500 ms / 45 px) and the
`dblclick` event, and — **only in the installed app** — locking the scale in the
`viewport` tag. That last one is not applied in an ordinary browser: disabling
zoom there would be an accessibility problem. Input fields and dropdowns are
excluded from the cancellation, without which a second nearby tap would stop them
opening. Pinching stays possible in the browser, and is blocked only in the app.

**Non-scrolling mobile header** — `#hero` carries `overflow-y:auto` for the
desktop column, where it genuinely helps when the window is short. On mobile,
where the header is fixed-position and auto-height, that value made it a useless
scrolling area: a slide on the planet "bounced" instead of doing nothing. The
rule is therefore cancelled below 880 px.

**One colour per unit** — the same across the whole game, tiles included:

| | Colour |
|---|---|
| Ore | gold `--gold` |
| Antimatter | violet `--violet` |
| Output per second | cyan `--cyan` |
| Global multiplier | green `--green` |

The multiplier, which occupied gold, was moved to green: it is not a unit but a
result, and gold goes back to ore, which it already designated in every price. A
price paid in antimatter carries the `.cost.am` class and therefore turns violet;
a "MAX" is no longer a price and turns green.

Green applies to **anything that is a multiplier**, wherever it appears: the
tile, the achievement bonus at the head of its tab, the antimatter bonus in the
cycle panel, and the temporary buff chips under the planet — those were gold
although they have nothing to do with ore. The properties of antimatter itself
(the "+17 % per unit", the exponent) stay violet: they describe the resource, not
its result.

**Owned levels** — in **Research** and **Automation**, the bottom-right corner of
each card shows the level owned (`3/15 levels`, `✓ owned` for a single-tier
automation), exactly where the Mining tab shows the number of units. What was
missing in antimatter is no longer displayed there: the greyed-out card and the
progress bar were already saying it, and the owned level is the information the
eye is really looking for. The **Upgrades** tab is unaffected, its purchases
being one-off.

**Statistics** — the tab is split into six sections (`STATCATS`), each showing
its values as **tiles**: label in small type above, value in large below, and a
colour bar on the left specific to the section. The old label-left / value-right
layout became unreadable on a wide screen, where the two ended up nearly 900 px
apart. The content is declared in the `STATS` array: each entry carries its
category, its bilingual label and the function computing its value. It notably
includes the **breakdown of anomalies by type**, invisible anywhere else in the
game.

**Language** — dropdown at the top right: closed it shows only the code
(**EN** / **FR**), open it lists the code and the name, each name written in its
own language ("English", "Français") — the only way for someone who does not
understand the displayed language to find their own. Your choice is remembered.

A **flag** version existed in 3.0.28-dev.7, as SVGs drawn inside the file rather
than emojis (Windows does not render flag emojis: 🇫🇷 shows as "FR" there, and
elsewhere their look depends on the system font). It was dropped for a
composition reason, not a technical one: two blocks of vivid colour in an
otherwise monochrome toolbar drew far more attention than a language selector
deserves.
**English is the default**: page title, installed app name, document `lang` and
description. The game switches to French automatically when the browser language
starts with `fr` (`fr`, `fr-FR`, `fr-CA`…), and stays in English for every other
one. Changing language does not touch the run in progress.

---

## Version

The version number is defined at the top of the `<script>`:

```js
const VERSION="3.0.28";
```

It is shown next to the title on desktop, and in a tile of the **Statistics** tab
at every screen size. It is the simplest way to check which version is actually
being served, since the service worker may keep a page in cache.

No build date appears there: a hand-written date always ends up lying, and the
version number is enough to identify a release.

**Increment rule** (`MAJOR.MINOR.PATCH`):

| Part | When to increment it | Examples |
|---|---|---|
| **MAJOR** | visible overhaul or change to the game's rules | moving to the sticky header, a new currency |
| **MINOR** | new feature, new content, balance | new anomaly, new achievements, changed probabilities |
| **PATCH** | bug fix, text or layout touch-up | shortened label, fixed overflow |

A single release only advances one level — the highest one concerned — and resets
those to its right: after `2.0.0`, a fix gives `2.0.1`, new content `2.1.0`.

The full history is in [`CHANGELOG.en.md`](CHANGELOG.en.md), with one release
note per version.

---

## Changing the balance

Everything is gathered at the top of the `<script>` in `index.html`:

| What you want to change | Where |
|---|---|
| Structures, prices, output | `GENS` array |
| Price growth (1.15) | `GROWTH` constant |
| Upgrade tiers | `TIERS` array |
| Click / global upgrades | `UPS.push(...)` calls |
| Research | `RES` array |
| Achievements | `ACHS` array |
| Achievement categories | `ACHCATS` array |
| Statistics content | `STATS` and `STATCATS` arrays |
| Anomalies, effects and **probabilities** (`w`) | `ANOMS` array |
| Anomaly frequency | `anomInterval()` |
| Prestige gain | `amGain()`, `AM_SEUIL`, `AM_EXPG` |
| Automations, prices, rates | `AUTOS` array, `CONTRE_S`, `SONDE_S` |
| Spending cap steps | `PARTS` array |
| Bonus per antimatter | `amBonus()`, `amMult()`, `AM_EXP` |
| Offline | `offlineCap()`, `offlineRate()`, `GRACE` |
| Texts of both languages | `T` object |
| Version number | `VERSION` constant |
| Licence shown in the game | `LICENCE` constant |

The `w` weights in the `ANOMS` array total 200: one point is worth 0.5 %.

---

## Licence

**GNU General Public License v3.0 or later** (`GPL-3.0-or-later`). The full text
is in the [`LICENSE`](LICENSE) file.

What it means, in plain terms:

- You may **use, copy, modify and redistribute** the game, including
  commercially.
- Any modified version you **distribute** must also be released under GPL 3, with
  its source code and a note of the changes.
- It is therefore impossible to make a closed version of it, or to embed it in
  proprietary software.
- The game is provided **with no warranty whatsoever**; the author is not liable
  for any damage related to its use.

A practical point specific to this project: the game is a readable HTML file
served as-is to the browser. The source code *is* what the visitor receives — a
plain "view source" is enough to check that a derived version respects the
licence.

The licence covers the **code**. It covers neither the name "Colonie Orbitale" /
"Orbital Colony" (trademark law, separate) nor the idea of the game: someone
rewriting an equivalent game from scratch is not bound by it.

To apply it under your full name, replace `Guilhem` in the header of
`index.html`, in that of `sw.js` and in the section above.
