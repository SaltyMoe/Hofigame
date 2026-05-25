# Hofigame – CLAUDE.md

## What This Is
A birthday surprise browser game for "Hofi" (bald, glasses, mountain bike rider).
Built as a **single `index.html`** file — pure JS + Canvas, no frameworks, no server needed.

## Deliverable
`index.html` in this repo root. Must run via `file://` in any desktop browser.

---

## Full Game Spec (as implemented)

### Main Character: Hofi
- Bald head, glasses, sporty MTB rider
- Controls: Arrow keys = drive, Space/↑ = jump
- Special: K (in air) = kickflip (Level 2+), P = protector (Level 4 boss)

### Level 1 – "Aschaffenburg" (Easy)
- Platforms with gaps, stones + bushes as decos
- **Helena + Clara** (children on bikes) at start — first collision shows Stephie warning
- **Mona** (apron, rolling pin) on later platform: "MTB is gruselig, ich backe lieber" — obstacle
- **Katy** on racing bike (Rennrad, aero tuck): "Rennrad > MTB" — obstacle (different platform from Mona)
- Green/pastoral atmosphere

### Level 2 – "München" (Medium)
- **Niko** stands physically before the kickflip gap (cx:465) with bubble: "Mach einen Kickflip! (drücke K in der Luft)"
- K key while airborne = kickflip (360° wheel spin, body rises separately, slight height+forward boost)
- 370px gap between platform 2 and 3 — requires kickflip to cross
- **Johanna** lying crashed on platform 3 (cx:980): "Ich bin gestürzt, aber kein Problem, morgen 150km Radtour" — obstacle
- **Velomobil person** in yellow aerodynamic capsule (cx:1280): thought bubble "Bald KPMG Gehalt hehehe, da kann man sich schon Mal was gönnen" — obstacle
- Background: Frauenkirche + Olympiaturm silhouettes, Richtung-Samerberg signs
- City/grey palette

### Level 3 – "Innsbruck" (Hard)
- Enemies: FPÖ voters patrol platforms ("MTB verbieten!", "Wege nur zum Wandern!") — touch = restart
- Signs: "Innsbruck", "Höttinger Alm", "Umbrüggler Alm"
- Background: Schloss Ambras silhouette, mountain shapes
- Alpine palette

### Level 4 – "Ligurien" (Hardest — Boss Level)
- Hofi spawns at startY:240, falls from shuttle bus at start
- **4 platforms** with 80px gaps (must jump 3 gaps before boss)
- **Simon** on bar stool (cx:710): "Mein Bike ist schon wieder kaputt, Tre Campari Spritz, per favore." — obstacle
- **Campari Spritz glass** (cx:745) — separate obstacle after Simon
- Background: **Johannes** flies past periodically: "Ich will mehr Pizza"
- **GYMMOE** boss at x:1100 — very wide torso, curly hair, lazy speech bubbles
- Walking into GYMMOE = defeat → hint screen
- Secret: **P** = Protektor → GYMMOE launches UPWARD off screen
- After boss gone: goal flag at x:1400, walk to it → Win

### Defeat Hint Screen (vs GYMMOE)
> "Wenn du gewinnen willst, musst du den Anfangsbuchstaben deines Geschenks drücken.
> Kannst du es erraten? Es gibt dir eine breitere Brust um den Gymmoe abzudrängen."

### Win Screen
Big banner + confetti. Names list:
> Corinna, Jakob, Johanna, Johannes, Katha, Katy, Felix, Simon, Simon, Simi, Moe, Mona, Niko, Stephanie

---

## Architecture

Single `<script>` block:
```
CONFIG      – W=800, H=450, GRAVITY=0.5, JUMP_FORCE=-12, PLAYER_SPEED=4, PW=28, PH=42
LEVELS      – array: platforms, decos, enemies, events, children, npcs, goalX, worldW, hasBoss
INPUT       – keydown/keyup → keys map + justPressed set
SPRITES     – all draw*() functions (no images)
PHYSICS     – overlap(a,b) AABB
ENTITIES    – Player, EnemyNPC, Boss classes
CAMERA      – camX = clamp(player.x - W/3, 0, worldW-W)
PARTICLES   – Confetti class, 150 instances
SCREENS     – drawTitle, drawLevelDone, drawDefeatHint, drawWin
GAME        – loadLevel, update, restart, handleInput
LOOP        – requestAnimationFrame
```

### Key technical notes
- **Kickflip**: K in air → `flipAngle += 0.18` per frame, full 2π = one flip; body rises (`bodyLift = -sin(flipT*π)*16`), wheel coin-flips (`scale(1, cos(kAngle))`); slight vy boost + 2× vx forward (needed to clear 370px gap)
- **GYMMOE bounds**: `{x:this.x, y:0, w:this.w, h:H}` — full canvas height, can't jump over
- **GYMMOE pushed**: `launchVy -= 0.4` per frame → accelerates upward; offScreen when y < -300
- **NPC system**: `npcs` array in level data `{cx, type, obstacle, gy}`; hitboxes computed per type in update(); drawn in drawPlaying() with type-specific draw fn + bubble
- **Thought bubble**: `drawThoughtBubble(bx,by,text)` — cloud shape (bumpy top via arcs) + 3 dot chain below
- **Children (Level 1)**: drawn via `children` array, separate from npcs; Stephie warning on first Helena/Clara hit
- **Dialog system**: events[] triggers dialog at trigX; auto-dismisses after 220 frames (only used in Level 1 now)
- **Win condition (Level 4)**: `bossDefeated && player.x >= 1400`

### Sprite functions
`drawHofi`, `drawEnemy`, `drawGymmoe`, `drawNikoPeek`, `drawStephiePeek`, `drawChild`, `drawMona`, `drawKaty`, `drawJohannes`, `drawNikoStanding`, `drawJohanna`, `drawVelomobil`, `drawSimon`, `drawCampariGlass`, `drawBubble`, `drawThoughtBubble`, `drawDeco`, `drawFlag`, `roundRect`

---

## Status
- [x] Repo initialized, pushed to GitHub
- [x] Level 1 – Aschaffenburg: Helena, Clara, Mona, Katy
- [x] Level 2 – München: Niko standing NPC, kickflip gap, Johanna crashed, Velomobil, city background
- [x] Level 3 – Innsbruck: FPÖ patrol enemies, Schloss Ambras, signs
- [x] Level 4 – Ligurien: shuttle start, 4 platforms, Simon+Campari, Johannes flyby, GYMMOE boss, P=Protektor, win screen
- [ ] Playtesting / polish (next session)

## GitHub
Repo: `https://github.com/SaltyMoe/Hofigame` — branch `main`
