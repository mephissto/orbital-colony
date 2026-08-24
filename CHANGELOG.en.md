# Changelog

[🇫🇷 Français](CHANGELOG.md) · 🇬🇧 English

One section per version, newest first. Recent entries are detailed and ready to
paste into a GitHub release; older ones are summarised in one line in the
[final table](#earlier-versions).

Numbering rule (`MAJOR.MINOR.PATCH`): see the
[Version section of the README](README.en.md#version).

**Save compatibility** — loading does `Object.assign(fresh_state, save)`. Adding
a field is therefore always transparent, in both directions, including for
export/import. No released version has ever renamed or removed a field: **every
2.x save is still valid**.

---

## 2.21.4 — Online play links

- 🔗 **README** (FR and EN): added the two addresses where the game can be
  played online, [orbital-colony.mephissto.fr](https://orbital-colony.mephissto.fr/)
  and [mephissto.github.io/orbital-colony](https://mephissto.github.io/orbital-colony/).

No change in the game itself.

---

## 2.21.3 — The tab bar stops moving vertically (for good)

- 🐛 **Bug fixed**: `touch-action:pan-x` had been set on `<nav>` since 2.15.1,
  but every tab also got its own inline `touch-action:manipulation`, set by
  the generic function used for every clickable element in the game. Since a
  tab covers almost the entire width of the bar, a finger almost always
  touches the tab itself rather than the space around it — its own setting
  won out, and a slightly diagonal drag on a tab could still scroll the page
  vertically.
- Every tab now gets `pan-x` just like its bar, for good.

---

## 2.21.2 — Rich vein and time leap now ignore an active power surge

- 🐛 **Bug fixed**: the rich vein and the time leap computed their gain as
  `output/s × duration`, but the output/s used included an active power surge.
  A ×10 surge caught just before multiplied the following vein's or leap's
  gain by 10 — handing out several times the intended amount at once, against
  the time leap's own rule ("never grants power you do not already have, only
  time ahead").
- Both now compute their gain from **base** output, excluding temporary buffs.
  Nothing else changed: the output display, clicks, and the offline bonus
  still include active buffs, as intended.

---

## 2.21.1 — Bilingual documentation

- 🇬🇧 **README translated into English** ([`README.en.md`](README.en.md)), with a
  language switch at the head of both files.
- 📄 **Separate changelog** ([`CHANGELOG.md`](CHANGELOG.md) and
  [`CHANGELOG.en.md`](CHANGELOG.en.md)): one section per version, ready to paste
  into a GitHub release. The history leaves the README, which now links to it.

No change in the game itself.

---

## 2.21.0 — Antimatter balance and four exploits fixed

### Exploits fixed

Measured on a complete run (20,000 antimatter, everything maxed out):

- 🔁 **Anomaly buffs stacked.** Four ×10 power surges caught back to back gave
  **×10,000 on output**, and a click buff on top pushed it to ×490,000. Only one
  buff is now active at a time, output and click alike: a new one replaces the
  previous.
- 🛰️ **The click buff amplified the satellites.** A click being worth 0.4 × your
  output, a ×12 quantum echo over ten automatic clicks per second was worth
  **×49 on total output**, for doing nothing. It now applies only to the player's
  own clicks — by hand, at 5 clicks/s, it still yields the equivalent of 24 times
  your output.
- 📦 **The starter capsule handed out free antimatter.** Its ore counted as
  *mined*: at level 6, every cycle started with **5 antimatter earned before
  playing a single second**.
- ♻️ **The auto-restart threshold did not follow progression.** Set to 50 and
  forgotten, it triggered a cycle per frame once the stock reached 100,000 —
  measured at **600 cycles and +75,400 antimatter in one minute**. A floor at
  10 % of your stock now applies, and the panel shows the threshold actually used.

The worst passive case drops from **×490,000 to ×5**.

### Miscellaneous

The "Perfect resonance" achievement required two simultaneous buffs, now
impossible: it asks instead for catching a buff while another is still running.

---

## 2.20.0 — A longer curve, automation follows

- ⚛️ **Antimatter gain exponent lowered to 0.32**, and the threshold for the
  first unit brought back from 20 to **10 billion** ore: it was the top of the
  curve that needed stretching, not the early game.
- 🤖 **Automation prices divided by 3.3**, to follow the new income. Automating
  everything costs 31,540 antimatter instead of 104,650.

| Antimatter | 2.18 | 2.19 | **2.20** |
|---|---|---|---|
| 1,000 | 18 s | 4.7 min | **9 min** |
| 20,000 | 20 s | 9.8 min | **47 min** |
| 100,000 | 22 s | 27 min | **1.5 h** |
| 1,000,000 | 26 s | 1.8 h | **18.8 h** |

---

## 2.19.0 — The antimatter gain changes formula

```
gain = ⌊ ( cycle ore ÷ 2e10 ) ^ 0.35 ⌋      instead of   12 × √( ore ÷ 1e12 )
```

Measured in simulation, a cycle yielding +50 % lasted **about twenty seconds at
every scale** — from 100 to 1,000,000 antimatter. Antimatter was effectively
free, and no research price could fix that.

The cause was not the threshold but the exponent: a cycle's length is set by
**rebuying the structures**, not by the antimatter threshold. Multiplying the
threshold by 16 only took a cycle from 21 to 40 seconds.

---

## 2.18.0 — Research prices reworked

Growth of at least **×1.8** and higher base costs, so that each level visibly
costs more than the previous one **from the very first**. The old scale started
at 4 antimatter with ×1.55 growth, giving 4 → 7 → 10: the progression was there,
but invisible to the eye on such small numbers.

Completing everything costs **234,890 antimatter** instead of 106,434.

---

## 2.17.4 — Automation, achievements and interface overhaul

Released version, cumulating everything since 2.0.0.

### New

- 🛰️ **Automation tab** — five automations paid in antimatter, kept from one
  cycle to the next, switchable at will: Mining satellites (10 levels), Foreman,
  Engineer, Recovery probe, Auto cycle.
- 🏆 **71 achievements** instead of 44, sorted into eight categories with their
  progress. Including one achievement per anomaly type, with thresholds matched
  to their probabilities.

### Balance

- ⚛️ **The antimatter bonus is no longer linear**: `(1 + am × bonus)^1.5`.
  At 1,000 antimatter, ×2,236 instead of ×171.
- 🎲 **Random anomalies**: each anomaly rolls its value on every appearance, and
  displays the amount obtained.

### Interface

- **Tab bar** rebuilt: one icon per tab, full labels everywhere, horizontal
  scrolling.
- **Statistics** as tiles grouped by theme, with the breakdown of anomalies by
  type.
- **One colour per unit**: ore gold, antimatter violet, output cyan, multiplier
  green.
- **Owned level** in the bottom-right corner of Research and Automation cards.
- **Satellites in orbit** around the planet, one per automatic-click level.

### Fixes

- Double-tap zoom on mobile, locked down for good in the installed app.
- The mobile header is no longer a scrolling area; the tab bar no longer moves
  vertically.

### Project

**GPL 3.0 or later** licence.

---

## Earlier versions

| Version | Content |
|---|---|
| 2.17.3 | the automatic click becomes the **Mining satellites** (🛰️), with both matching achievements renamed |
| 2.17.2 | last multipliers turned green: achievement bonus, cycle panel bonus, temporary buff chips |
| 2.17.1 | satellites at fixed speed, with a pulse, and orbit recalibrated so the dots no longer overlap neighbours |
| 2.17.0 | the pulse wave is replaced by orbiting satellites, one per automatic-click level |
| 2.16.0 | cyan wave on the planet and a blinking dot on the card, at the automatic-click rate |
| 2.15.2 | inactive tabs become visible again, muted, and the active tab gains a cyan top edge |
| 2.15.1 | the tab bar no longer moves vertically on touch: gesture limited to horizontal, recentring without `scrollIntoView` |
| 2.15.0 | owned level in the bottom-right of Research and Automation cards; one colour per unit across the game |
| 2.14.0 | tab bar rebuilt: one icon per tab, full labels everywhere and horizontal scrolling with edge fades |
| 2.13.2 | double-tap zoom: three barriers instead of one; the mobile header is no longer a scrolling area |
| 2.13.1 | single-tier automations show "Price" instead of "Price of level 1" |
| 2.13.0 | the automatic click starts at 100 antimatter instead of 30 (still ×2 per level) |
| 2.12.1 | the project moves to the GPL 3.0-or-later licence: `LICENSE` file, headers, "Licence" tile |
| 2.12.0 | statistics screen rebuilt as tiles grouped by theme; "Lightning reflex" achievement (71 total) |
| 2.11.0 | 5 more achievements (70 total): 100,000 and 1,000,000 clicks, click power up to 1 Sx, and 1,000 anomalies |
| 2.10.0 | 13 more achievements (65 total): four click tiers and nine on anomalies, including one per type |
| 2.9.0 | achievements sorted into eight categories, and eight automation achievements added (52 total) |
| 2.8.0 | the automation settings become two self-contained boxes, and the spending cap becomes a dropdown |
| 2.7.0 | spending cap in 10 % steps; cycle restart threshold typed by hand |
| 2.6.0 | both automation settings move from percentages to three named modes |
| 2.5.0 | automatic click up to level 10; Foreman at 300 and Engineer at 450 antimatter |
| 2.4.0 | Foreman, Probe and Auto cycle move to a single tier |
| 2.3.0 | **Automation** tab: five automations bought with antimatter and switchable at will |
| 2.2.0 | the antimatter bonus is no longer linear: the total is raised to the power 1.5 (`AM_EXP`) |
| 2.1.0 | every anomaly rolls its value at random; the badge and message show the amount obtained |
| 2.0.0 | consolidated public version: installable PWA, bilingual FR/EN, fixed mobile header, 44 achievements |
| 1.0.0 | first numbering, introduced together with the version display |
