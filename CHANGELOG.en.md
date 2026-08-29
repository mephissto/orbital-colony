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

## 3.1.1 — The menu opens on touch

- 🐛 **On a phone, the gear opened nothing.** After a tap the browser synthesises
  a `click` and places it on whatever is under the finger at that moment — that
  is, on the backdrop of the window that had just opened. The "click outside to
  close" behaviour (3.0.0-dev.17) took it for a real click and closed the window
  right away. **Menu, changelog, about, export and tutorial were all affected.**
- Closing on an outside click now requires **both the press and the release** to
  happen on the backdrop. Welcome side effect: dragging from inside a window and
  releasing on the backdrop no longer closes it.
- With a mouse the bug did not exist — `bindAction` acts on `pointerdown` there,
  and a `click`'s target is the common ancestor of mousedown and mouseup, never
  the backdrop. The whole test suite drove these windows with a mouse: it is now
  doubled with a **touch** test.

No save field touched.

---

## 3.1.0 — Challenge achievements

- 🎯 **Seven more achievements**, in a new **"Challenges" category**: one per
  challenge beaten (*Dead air*, *No hands*, *Crash averted*, *Small but mighty*,
  *Patched up*, *Ex nihilo*) and a last one for all six, *Rule breaker*.
- They are **built from the challenge list** rather than copied beside it: adding
  a challenge will add its achievement, and the two lists cannot drift apart.
- The achievement bonus therefore goes from **+71 %** to **+78 %** output in
  full.

No save field touched: challenge achievements are derived from `chalDone`, which
has existed since 3.0.28.

---

## 3.0.28 — Challenges

The first of the two big steps planned to lengthen the game. Six **challenges**
each break one rule of the game for a whole cycle, with a goal computed from your
best cycle and a permanent reward.

### The challenges

- 🎯 New **Challenges** tab, visible from the first cycle but **greyed out**,
  with the remaining-cycle countdown. It opens when the header tile reads
  "cycle #6 in progress".
- Entering **banks the current cycle first** and credits the antimatter you had
  pending, then resets ore, structures and upgrades. Kept: antimatter, research,
  achievements, automations and the rewards of challenges already cleared.
- A permanent banner shows progress and a **Leave** button, with no penalty.
  Once beaten, a challenge is cleared **for good**.
- During a challenge, **only the ♻️ cycle restarter is suspended** — it would
  wipe your progress. Every other automation keeps working. Research cannot be
  bought; the tab is greyed out and says why.

| Challenge | Broken rule | Permanent reward |
|---|---|---|
| 📵 Radio silence | no anomaly appears | anomalies 20 % more frequent |
| ⛓️ Hands tied | clicking yields nothing | output +25 % |
| 📈 Inflation | prices ×1.35 instead of ×1.15 | −8 % on every price |
| 🏚️ Dwarf colony | only 6 structures, but they produce ×4 | the last 4 produce ×2 |
| 💨 Containment leak | output −2 % every 2 min, floor at 20 % | antimatter gain +15 % |
| 🕳️ The Void | antimatter no longer counts in the multiplier | antimatter exponent 1.50 → 1.55 |

All six were balanced by **second-by-second simulation of a real save**,
anomalies, buffs and automations included, each challenge compared to an ordinary
cycle aiming at the same goal: between 2.4 and 3.8 h for an attentive player, or
2.5 to 5 times a normal cycle. Two of them were **arithmetically impossible**
before that measurement — the leak bounded the cycle's total ore, and the dwarf
colony could not approach a record set with ten structures. Details in
[ROADMAP.md](ROADMAP.md).

### Clicking finally matters early on

A **7 % base echo** now exists before any resonator: without it, a click went
from 10 s of output down to **0.006 s** within an hour on a fresh run. It is now
worth ~0.10 s permanently.

Resonators rise to 9/12/15 % and the click multipliers drop to ×1.4 / ×1.5 /
×1.6 / ×1.7 (×5.71 in full). The late-game ceiling does not move at all —
0.15 × 5.71 = **0.86 s of output**, the previous value: click power is simply
moved towards the early game.

### One button, one menu

- ⚙️ The three top buttons (Save, Export/Import, Reset) are grouped behind a
  **gear**. The menu also holds a **tutorial**, an embedded **changelog** and an
  **about** panel (source code, licence, Patreon).
- 🎓 The **tutorial** opens by itself on the first run — five screens, with arrows
  and swipe. A run already in progress does not trigger it.
- 🛰️ A **lore** opens the tutorial and justifies the game's name: the colony
  turns around a dead planet, the orbit decays, and every colony falls back in
  the end. "An orbital colony is never finished, it is only falling more slowly
  than the last one."
- Every window closes on a click outside — except the confirmation, which is not
  cancelled by a stray click.

### Interface

- 🏗️ The **Foreman** buys in batches of **×1, ×10 or ×25**.
- 📌 The **×1 / ×10 / ×100 / MAX** row stays stuck at the top while you scroll the
  structures.
- 🌐 **English becomes the default language**: page title, installed app name,
  document `lang` and description. French is still auto-detected. The selector
  becomes a dropdown.
- 📊 The **Ore** tile shows `cycle · total`, the **Antimatter** tile shows the
  current cycle and what is still missing for the next antimatter. The Research
  tab now counts the **current** cycle rather than completed ones.
- 🔴 On `dev.` the header turns red, with a **DEV** pill and a prefixed title — so
  the two tabs can no longer be confused.
- 🔢 The version number stays visible at every width.

### Compatibility

One field added (`tuto`), plus the challenge ones (`chal`, `chalDone`,
`chalStart`, `chalBut`, `bestRun`, `autoQte`). **Every 2.x save is still valid**
and does not trigger the tutorial.

---

## 2.35.3 — The Foreman buys, on two lines

- 🏗️ In the automation settings, **"The Foreman rebuys" becomes "buys"** — nothing
  is being bought back, it is one more purchase, every second. Same fix on the
  card's description.
- ↩️ The status line now spans **two lines**: the target price, then what is left
  to go. On a small screen the wrap landed in a different place depending on the
  number shown; it is now always in the same place.

No save field touched.

---

## 2.35.2 — A box to stop being asked

- ☑️ The install dialog gains a **"Don't remind me on this device"** box. Ticked
  before "Close", it also removes the **📲 pill**: nothing comes back for players
  who want to stay in their browser.
- 🧠 The choice stays **per device** (`localStorage`, outside the save), which now
  counts three possible refusals: *later* (pill), *never* (nothing), and
  *installed* (nothing).
- 🔁 The box is **unticked every time the dialog opens**: reopening it from the
  pill must not turn a temporary refusal into a permanent one by accident.
- 🏷️ The **"Later" button becomes "Close"**: ticking "don't remind me" and then
  confirming with "Later" no longer made sense.

No save field touched.

---

## 2.35.1 — The iOS warning gets its own line

- ⚠️ In the install dialog, "It has to be Safari: other iOS browsers do not offer
  it" now sits **on its own line**, prefixed with ⚠️ — it is the classic trap and
  should not get lost at the end of the instructions.
- 🔧 Translated labels all went through `textContent`, so a `<br>` would have
  shown literally. New `data-i18n-html` attribute, switching `applyI18n()` to
  `innerHTML` for the few labels that carry markup.

Display fix only.

---

## 2.35.0 — The game offers to install itself on mobile

- 📲 A dialog invites players on mobile who are going through the browser to
  **install the game as an app**. It carries the **Android** and **iPhone /
  iPad** instructions side by side.
- 🎯 It appears **neither on desktop nor for anyone already in app mode** —
  detected through `display-mode: standalone` and, for Safari iOS which does not
  follow the standard, `navigator.standalone`.
- ⚡ On **Chrome/Android** the `beforeinstallprompt` event is captured: an
  **Install** button is added and fires the real system dialog, skipping the
  instructions entirely. iOS exposes nothing equivalent, hence both tutorials
  shown together rather than one guessed from the user agent.
- 🔁 Declining leaves a **📲 pill at the bottom right**, which reopens the dialog
  on demand. The refusal is remembered **per device**, in a `localStorage` key
  separate from the save: it does not follow an exported run and does not come
  back after an import.
- 🐛 Two display fixes found while testing: modal buttons were squashed to
  34×34 px below 720 px wide — a rule meant for the toolbar icons alone, now
  scoped to it — and the toast strip reserves the pill's width so as not to cover
  it.

No save field touched.

---

## 2.34.0 — Foreman and Engineer swap prices

- 🔄 The **Foreman goes from 100 to 150 antimatter**, the **Engineer from 150 to
  100**. The two are mutually exclusive, and the one you actually keep switched
  on is the Foreman: it rebuys structures continuously, whereas the Engineer's
  job is **finished** once all 73 upgrades are bought. The more useful one had to
  be the more expensive.
- 📋 They also swap places in the list, so it stays in ascending price order:
  30 → 100 → 150 → 200 → 400.
- 💰 **The automation total does not move** (31,540 antimatter): this is a swap,
  not a rise.

No save field touched; an automation already bought stays bought.

---

## 2.33.1 — Version number

No code or balance change. Version bumped to mark the 2.31 → 2.33 release as a
whole.

---

## 2.33.0 — Antimatter gain returns to its former pace

- ⚖️ The click fixes (2.30 → 2.32) had, **without that being the goal**, nearly
  doubled the ore of an advanced cycle — and therefore the antimatter gain:
  **69.9K per cycle against 36.2K** originally. The gain exponent (`AM_EXPG`)
  goes from **0.32 to 0.30** to cancel that inflation.
- 🎯 The exponent rather than the threshold, because it **leaves the first
  prestige untouched** — 1 antimatter either way — and corrects all the more
  strongly as the cycle grows, exactly where the inflation happened: mid-game
  83 → 63, advanced 1.58K → 999, very advanced 69.9K → 34.8K.
- 🕳️ What is really at stake: antimatter has a single outlet, the 8 researches
  (234,890 in total). Doubling the gain meant **halving the time before it
  becomes useless**. Maxing the researches goes from **4 cycles to 7** for a very
  advanced player.
- A fully equipped player is back to the pre-2.30.0 yield (×0.96). A player who
  does not automate their clicks stays about 30 % below — a case deliberately set
  aside, since the Satellites are long since owned at that stage.

No save field touched: antimatter already earned is not taken back.

---

## 2.32.0 — Servo-assisted arms finally do something

- 🤖 The **Servo-assisted arms** research gave ×2 click power per level, but **on
  the strike alone** — a base of 1 that never grows. Measured: an advanced player
  restarting a cycle ended up with **exactly the same ore and the same output
  after 5 minutes with 0 or 12 levels**. 8,675 antimatter for nothing, and it is
  the most expensive research after Mining optimisation and Resonance.
- ✖️ It now gives **+8 % per level on the whole click**, i.e. **×2.52** at
  maximum. Every level is felt, from the first to the twelfth.
- 💰 **The price ladder does not move by a single antimatter** (6 → 3,857, 8,675
  total). That is what keeps the research consistent with the other seven, whose
  first levels all cost between 6 and 30: a base raised to 150, considered for a
  moment, would have been five times the most expensive in the game.
- 📈 Over a 30-minute cycle with ten satellites: **50.1K antimatter** at 0/12
  (same as before), **59.1K** at 6/12, **69.9K** at 12/12. The maxed research
  therefore pays **+39 % per cycle**, against 0 % before.
- The click peaks at **2.18 s of output** per click, reached only with all twelve
  levels paid for.

---

## 2.31.0 — A click never exceeds output again

- 📏 **New balance rule: a click must never be worth more than one second of
  output.** 2.30.0 broke it by a wide margin — the click reached **6.40 s of
  output** late in the game.
- ✖️ Click upgrades move from ×2 across the board to **×1.5 / ×1.6 / ×1.8 / ×2**
  (×8.64 in full instead of ×16), with a progression that finally follows the
  price.
- 📡 **Resonators** drop from 3/12/40 % to **2/5/10 %**. The two go together: at
  40 % the click was already worth 0.40 s of output **before any click upgrade**,
  so no multiplier above ×2.5 could be added without breaking the rule.
- 📊 Measured result: the click **peaks at 0.86 s of output** (against 6.40 in
  2.30.0 and 0.40 originally), while staying **2.15× stronger than it originally
  was**. Each upgrade keeps its exact gain — ×1.50, ×1.60, ×1.80, ×2.00 — right
  to the end of the game, which was the whole problem in the first place.
- ⏱️ Over a simulated 30-minute cycle with ten Satellites, antimatter earned goes
  from 101K (2.30.0) to about **50K**, against 36K for the original behaviour —
  the gap drops from ×2.8 to ×1.4.

No new save field.

---

## 2.30.0 — Click upgrades: one number, on the whole click

- ✖️ The four click upgrades are now worth **×2 each, on the click's total
  value**, resonator echo included. The card says "Click ×2" and it means exactly
  ×2, however far along you are. All four: **×16**.
- 🧹 The double effect from 2.29.0 (×N on the strike + resonance points) is
  dropped: it did fix the underlying problem, but **two numbers on a card, one of
  which only applies conditionally, do not read**.
- ⚖️ Accepted rebalance in both directions: all four give ×16 instead of ×480
  very early on (weaker click at the start), and **3.04M instead of 328K** late
  in the game on the reference scenario.
- 💰 All four give the same ×2 despite very different prices: doubling a large
  click is already worth far more in absolute terms than doubling a small one —
  that is what the rising price buys.
- **Servo-assisted arms** stay on the strike only (×4096 on the whole click would
  be out of scale): like the whole strike, that research matters less and less as
  the echo takes over.

No new save field.

---

## 2.29.0 — Click upgrades now strengthen resonance too

- 🖱️ The "**click power**" upgrades only multiplied the first term of the click
  formula — `1 × multipliers × global multiplier` — a base of **1 that never
  grows**, whereas the resonator term follows your output. As soon as raw output
  passes a few thousand/s, the first term is drowned out. Measured by buying the
  **Magnetic field ×8**: **×7.33** early on, **×1.31** at medium output with
  resonator v2, and **×1.00** with v3 and a large output — for 2 billion ore.
- ➕ Each one now also adds **resonance points** on top of its ×N: Ion hammer
  **+2**, Exoskeleton **+4**, Capacitor **+8**, Magnetic field **+15**.
  Resonator v3 alone gives 40 %, v3 with all four click upgrades gives **69 %**.
  The Magnetic field is now worth **×5.71 / ×1.71 / ×1.28** depending on how far
  you are: always noticeable, without upending the economy.
- 🚫 The points **only apply if you own a resonator** — without one there is no
  resonance to strengthen, and the raw ×N is plenty at that stage.
- 📝 The cards state both effects ("Click power ×8, resonance +15 points").

No new save field.

---

## 2.28.0 — Upgrades are rediscovered every cycle

- 🔎 An upgrade appeared in the list as soon as you had mined 8 % of its price
  **over the whole run** — a total that is never reset. As a result, from the
  second cycle on, **every upgrade not tied to a structure** (click, resonators,
  global output, beacon) showed up in one block within the first second, and the
  progressive rediscovery was gone for good.
- ♻️ The criterion now uses the ore mined **during the current cycle**. It stays
  monotone within a cycle — the list never jumps around while you play — but it
  resets at prestige, like the upgrades themselves, which are lost at that point.
- **Structure tiers** are unchanged: they also carry their "N units owned"
  condition, so they already reappeared as the cycle went on.

---

## 2.27.1 — No more blinking dot on the Satellites card

- 🔕 The small cyan dot pulsing on the **Mining satellites**' state line, in the
  Automation tab, is gone. The card already spells out "N satellites in orbit",
  and the dots orbiting the planet carry the same information: one more animation
  only distracted in a panel you come to read, not to watch.
- The **dots orbiting the planet are unchanged**, they keep turning and pulsing.

---

## 2.27.0 — The idle automation's switch is now locked

- 🔒 While one of the two is working, the other's switch is **greyed out and
  inert** (`not-allowed` cursor) — including when that one had been **switched
  off by hand** beforehand. Until now it was only locked if it had been
  suspended: you could therefore believe you had switched it back on when it
  would not actually have run. To hand control back, switch off the one that is
  running first.
- 🏷️ The label tells the two situations apart: "**paused by the Engineer**" when
  it will restart on its own (knob on the right), and "**off — the Engineer is
  working**" when you switched it off yourself (knob on the left). The knob's
  position therefore says what will happen once the other stops.
- 💬 Clicking a locked switch shows "Switch the Engineer off first to free this
  one" rather than silently doing nothing.

No new save field.

---

## 2.26.0 — Switching the Engineer off hands control back to the Foreman

- 🐛 **Bug fixed**: since 2.25.0 the exclusivity pause was stored as a **manual
  switch-off**. Switching the Engineer off therefore left the Foreman dark even
  though the player had never switched it off — merely suspended it — and it had
  to be switched back on by hand.
- 🔁 The two states are now distinct: **switched off by hand** (`S.autoOff`, the
  player's intent, which nothing clears automatically) and **paused**
  (`S.autoPause`, a derived state cleared as soon as the other stops). Switching
  the Engineer off therefore **hands control back to the Foreman** — but does not
  revive a Foreman you deliberately switched off.
- 🗃️ A save from before 2.26.0 is read back on load: if exactly one of the two is
  off while the other runs, that switch-off is reinterpreted as a pause.

Two new purely additive fields, `S.autoPause` and `S.autoMain` (which of the two
took over last). Earlier saves remain compatible.

---

## 2.25.2 — The pause now says who triggered it

- 🏷️ A suspended automation no longer reads "paused" but **"paused by the
  Engineer"** (or "by the Foreman"): the cause is named right where you read it,
  with no guessing which of the two took over.
- 📱 On narrow screens (< 520 px) the state line now moves **below the name**
  instead of disappearing. It had been hidden since 2.24.0 to save room — which
  made exactly this message invisible on a phone.

---

## 2.25.1 — A paused automation stays visibly armed

- 🎚️ The switch of an automation **paused by exclusivity** keeps its knob **on
  the right**, merely **greyed out**, instead of flipping left as if it had been
  switched off. It is armed, the game suspended it — and you no longer think you
  turned it off by mistake. Its row is also less faded than one switched off by
  hand.
- 📝 Both cards now explain it in their description: the Foreman "pauses the
  Engineer", the Engineer "pauses the Foreman".

Display fix only, no change in behaviour.

---

## 2.25.0 — Foreman and Engineer become mutually exclusive

- 🔀 The automatic ore sharing introduced in 2.24.0 (half the stock to the
  Foreman) gave good figures but **was not legible while playing**: you see your
  structures slow down without understanding why. The two automations are now
  **mutually exclusive** — **switching one on pauses the other**, the switch
  shows it, and you decide which one works.
- ⏸️ A row paused by exclusivity reads "**paused**", not "switched off": at a
  glance you can tell what you switched off yourself from what the game put on
  hold.
- 🆕 An automation you have just **paid for starts switched on** and takes over
  from its exclusive partner: no "I just bought it and it does nothing" surprise.
- ↩️ The Foreman has **no brake left**: it buys its target as soon as it has the
  price, full stop.
- 🗃️ A save from before 2.25.0 where both were running is fixed up on load: the
  **Engineer keeps control**, its job being finished once all 73 upgrades are
  bought.

No new save field.

---

## 2.24.0 — Foreman and Engineer now share the ore

- ⚖️ Since 2.23.0 the two automations were stepping on each other: the Foreman
  buys as soon as the ore covers its target, so the stock never grew and the
  **Engineer starved permanently** — no even mildly expensive upgrade ever became
  affordable. The Foreman now spends only **half the stock** while the Engineer
  still has something to buy, and all of it once it is done. No setting to
  understand. Measured over 30 simulated minutes in mid-game, Drone target:
  **15 upgrades and 56,267/s** instead of 13 and 37,515/s, for 2 fewer
  structures.
- 🚫 The **% spending cap** disappears from the UI: it only ever served that
  sharing, which the rule above now handles on its own. The `S.autoPart` field
  stays in the state so earlier saves and exports remain symmetric.
- 🎛️ The **switches move up into the Settings**, one row per owned automation
  (icon, name, state, switch). The list below now only serves for buying and for
  the Satellites' levels.
- ✂️ Setting explanations **shortened**.

An intermediate approach was tried then dropped: reserving the **exact price** of
the next upgrade pinned the Foreman at **zero purchases** without gaining the
Engineer anything — the stock never durably exceeds that price, since the
Engineer buys it the moment it is reached.

---

## 2.23.0 — The Foreman now targets the structure you choose

- 🎯 The **Foreman** always rebought the **cheapest** affordable structure. That
  is the opposite of what you do by hand: later structures pay off far more per
  ore spent, and letting the automation pile up drones wastes output. It now has
  a **dropdown** in the Settings, listing the structures **already revealed**
  with their icon: it only ever buys that one, one per second.
- 💰 It is **no longer subject to the % spending cap**. Aiming at a big structure
  would be pointless if the automation were not allowed to put all the required
  ore into it. The cap therefore now concerns the **Engineer** only, and its
  label says so explicitly.
- ⏳ If the target is not affordable it **buys nothing and waits** — no falling
  back on a cheaper structure, that is the whole point of "one at a time". The
  panel line gives the target price, what is missing and a time estimate, and the
  automation's card shows its target at all times.
- ⬆️ **Settings moved to the top of the tab**, before the automation list. Once
  those are bought the list no longer changes, whereas the panel is what you come
  back to: you had to scroll the whole tab for every adjustment.
- 🔒 The dropdown only offers structures you have already discovered (`S.seen`):
  nothing is disclosed early. Until you pick one, it aims at the **last revealed
  structure** — the best default, and it avoids an automation bought late piling
  up drones.

New `S.autoGen` field, purely additive: earlier saves load without conversion and
start on the default above.

---

## 2.22.0 — Owned upgrades are now sorted into categories

- 🗂️ The **Already owned** list in the Upgrades tab was one long flat queue in
  internal definition order: after 30 or 40 purchases you could no longer find
  anything in it. It is now split into sub-sections:
  - **one header per structure** for the tiers (🛸 Mining drone, ⛏️ Automated
    drill… in Mining-tab order);
  - then 🔨 **Click power**, 📡 **Click resonance**, 🔗 **Global output** and
    🔮 **Anomalies** for the other families.
- 🔢 Each header carries an `owned/total` counter (`4/6`, `2/4`…) that turns
  **gold** once the family is complete, just like in the Achievements tab.
- 🙈 A category only appears **once you own your first upgrade in it**. Showing
  "0/6" for the Dyson sphere on a first run would reveal its existence far too
  early; here the counter only tells you *how many* are left in a family you
  have already started, never which ones or what they do.
- The **Available** list is unchanged: still sorted by ascending price, which is
  what you actually look at when buying.

No change to the economy, balance or save format.

---

## 2.21.10 — Scroll arrows now follow the pointer, not the width

- ◀▶ The tab bar's scroll arrows (2.21.9) were limited to the desktop layout
  (width ≥ 881px). A narrow window on a computer — two games side by side,
  split screen — switches to the mobile layout while still being driven by a
  mouse or trackpad, exactly the case where they help the most. They now
  depend on the **available pointer** (`pointer:fine`, mouse/trackpad)
  rather than width: active in both layouts as long as there's no
  touchscreen, hidden as soon as the pointer is touch-based.

---

## 2.21.9 — Scroll arrows and fixed mobile gap

- ◀▶ **Scroll arrows on the tab bar** (mouse/trackpad, desktop only —
  touch already has swipe). They only appear on the side where tabs are
  actually hidden, and disappear automatically once you reach the end.
- 🐛 **Bug fixed**: on mobile, `#hero` (the planet banner) was pinned to a
  header height fixed at 53px. A header genuinely a bit taller — different
  font rendering across browsers/OS — left a visible gap between the top bar
  and the planet. `<header>`'s real height is now measured in JS and
  tracked through any change (rotation, language switch, etc.), no more
  fixed value.

---

## 2.21.8 — The actual cause of the tab bar jumping

- 🐛 **Bug fixed, properly this time**: `<nav>` had no `position` of its own,
  while its parent `<main>` is `position:relative` (the hero/panel grid).
  Without that, a tab's `offsetLeft` was measured relative to `<main>` — so
  offset by the width of the planet column (352px) — while the scroll
  calculation compared it against `nav.scrollLeft`, which starts at 0 at
  `<nav>`'s own edge. The target was therefore systematically far too large,
  and almost always got clamped to the maximum scrollable position by the
  safety limit: clicking any tab, even Upgrades right next to Extraction,
  sent the bar all the way to the end and hid Extraction. This was the real
  cause behind the previous two attempts (2.21.6, 2.21.7), which fixed
  symptoms without touching this shared root cause.
- `position:relative` set on `<nav>`: its tabs are now measured in the same
  coordinate space as its own scrolling.
- Along the way, `centrerOnglet()` now makes the **minimal** adjustment
  needed (bringing the hidden edge into view) instead of a full re-center, so
  the bar is never moved more than necessary.

---

## 2.21.6 — Clicking a visible tab no longer re-centers the bar

- 🐛 **Bug fixed**: `centrerOnglet()` re-centered the clicked tab on every
  click, even when it was already fully visible. On a narrow window (two
  games side by side, split screen), clicking Upgrades (2nd tab) still
  re-centered the view and pushed Extraction out of sight, even though
  nothing needed scrolling in the first place.
- The bar now only scrolls when the clicked tab is actually hidden, in whole
  or in part — a tab that's already visible stays put.

---

## 2.21.5 — The tab bar stops scrolling vertically (Mac / trackpad)

- 🐛 **Bug fixed**: `<nav>` only set `overflow-x:auto`, without specifying
  `overflow-y`. One axis cannot stay "visible" while the other scrolls: the
  browser then computes `overflow-y:auto` on its own. The bar also genuinely
  overflows by 1px in height (the tabs' `top:1px`), enough to make it
  scrollable vertically — with a mouse wheel or a trackpad on desktop, a case
  `touch-action:pan-x` (2.15.1, 2.21.3) didn't cover since it only applies to
  touch input.
- `overflow-y:hidden` is now set explicitly on `<nav>`.

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
