# Hofigame – CLAUDE.md

## What This Is
A birthday surprise browser game for "Hofi" (bald, glasses, mountain bike rider).
Built as a **single `index.html`** file — pure JS + Canvas, no frameworks, no server needed.

## Deliverable
`index.html` in this repo root. Must run via `file://` in any desktop browser.

---

## Full Game Spec

### Main Character: Hofi
- Bald head, glasses, sporty MTB rider
- Controls: Arrow keys = drive, Space = jump
- Special: K (in air) = kickflip (Level 2+), P = protector (Level 4 boss)

### Level 1 – "Aschaffenburg" (Easy)
- Stones + bushes as obstacles, small gaps
- Green/pastoral atmosphere
- No enemies, no special mechanics

### Level 2 – "München" (Medium)
- "Niko" pokes head from left edge of screen with speech bubble: **"Mach einen Kickflip!"**
- K key while airborne = kickflip animation (360° spin) + doubles jump distance
- One large gap that requires kickflip to clear
- Teaches the kickflip mechanic

### Level 3 – "Arzler Alm" (Hard)
- Enemies: FPÖ voters who want to ban MTB — patrol platforms, must be avoided
- Signs everywhere labeled **"Arzler Alm Trail"**
- Touch enemy = restart level
- Alpine palette

### Level 4 – "Ligurien" (Hardest — Boss Level)
- Boss: **GYMMOE** — very wide torso, many curls, blocks the path
- GYMMOE speech bubble cycles through:
  1. "Ahhh, heute bin ich zu faul mich hier zu bewegen."
  2. "Keine Lust"
  3. "Rülps mich nicht von der Seite an"
- Walking into GYMMOE = defeat → show hint screen

### Defeat Hint Screen (vs GYMMOE)
> "Wenn du gewinnen willst, musst du den Anfangsbuchstaben deines Geschenks drücken.
> Kannst du es erraten? Es gibt dir eine breitere Brust um den Gymmoe abzudrängen."

Secret: **P** = Protektor (chest protector for MTB)

### When P is Pressed (vs GYMMOE)
- Hofi sprite gets a visible chest protector (wider torso, dark plate)
- GYMMOE slides off screen to the right
- Level won → Win screen

### Win Screen
Big banner: **"Glückwunsch! Du hast gewonnen. Erhalte jetzt dein Geschenk."**

Then scrolling list (top to bottom) with confetti:
> Alles Gute zum Geburtstag wünschen: Corinna, Jakob, Johanna, Johannes, Katha, Katy, Felix, Simon, Simon, Simi, Moe, Mona, Niko, Stephanie

---

## Architecture (planned)

Single `<script>` block with section comments:

```
CONFIG      – canvas size, physics constants
LEVELS      – data arrays (platforms, decorations, enemies, events, goalX)
INPUT       – keydown/keyup → keys map + justPressed set
CAMERA      – camX follows player, world→screen translation
SPRITES     – all draw*() functions (no external images)
PHYSICS     – gravity, AABB collision helpers
ENTITIES    – Player class, Enemy class, Boss (GYMMOE) class
EVENTS      – zone-triggered dialog system
PARTICLES   – confetti for win screen
SCREENS     – title, levelComplete, defeatHint, win renderers
GAME        – state machine, level loader
LOOP        – requestAnimationFrame
```

### Key technical notes
- Canvas: 800 × 450 px
- GRAVITY = 0.5 px/frame²,  JUMP_FORCE = -12,  PLAYER_SPEED = 4
- Kickflip: activatable once per airborne phase (K key), multiplies vx × 2 for duration of jump
- GYMMOE boss: phase = 'blocking' → 'pushed' after protector; slides right at +3px/frame until off-screen
- All sprites drawn with canvas 2D primitives (arcs, rects, lines) — no images
- Confetti: ~150 particles, spinning colored rectangles, respawn from top when off-bottom

---

## Status
- [x] Repo initialized, pushed to GitHub
- [x] Game spec finalized
- [x] Architecture plan written
- [ ] `index.html` implementation — **TODO (next session)**

## Plan File
Full detailed plan: `.claude/plans/weiter-machen-tingly-rabbit.md` (local only, not committed)
