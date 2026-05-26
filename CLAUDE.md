# Hofigame – CLAUDE.md

## What This Is
A birthday surprise browser game for Hofi (bald, glasses, mountain bike rider).
Built as a **single `index.html`** file — pure JS + Canvas, no frameworks, no server needed.

## Deliverable
`index.html` in this repo root. Runs via `file://` or live at **https://saltymoe.github.io/Hofigame/**

---

## Full Game Spec (as implemented)

### Main Character: Lukas
- Bald head, glasses, sporty MTB rider (sprite: `drawLukas`)
- Controls: Arrow keys = drive, Space/↑ = jump
- Special: K (in air) = kickflip (Level 2+), P = protector (Level 4 boss)
- Jump instruction NOT shown on title — learned via Stephie warning in Level 1

### Level 1 – "Aschaffenburg" (Easy)
- Platforms with gaps, stones + bushes + trees as decos
- **Schloss Johannisburg** silhouette in background (cx:1100)
- **Helena + Clara** (children on bikes) at start — first collision shows Stephie warning ("Nutze Leertaste um zu springen")
- **Mona** (apron, rolling pin, cx:970): "MTB is gruselig, ich backe lieber" — obstacle
- **Katy** on racing bike (Rennrad, aero tuck, cx:1350): "Rennrad > MTB" — obstacle
- Green/pastoral atmosphere

### Level 2 – "München" (Medium)
- **Niko** stands before kickflip gap (cx:465): "Mach einen Kickflip! (drücke K in der Luft)"
- K key while airborne = kickflip — needed to cross 370px gap
- **Johanna** lying crashed (cx:980, gy:370): "Ich bin gestürzt, aber kein Problem, morgen 150km Radtour" — obstacle
- **Jakob** (Velomobil person, cx:1280): bubble "Bald KPMG Gehalt hehehe..." speaker='Jakob' — obstacle
- Background: Frauenkirche + Olympiaturm silhouettes, 2× Richtung-Samerberg signs
- Buildings sit on ground level (y:380)
- City/grey palette

### Level 3 – "Innsbruck" (Hard)
- Enemies: FPÖ voters patrol platforms ("MTB verbieten!", "Wege nur zum Wandern!") — touch = restart
- **Felix** (cx:150, Platform 1): alternating bubbles every ~5s (300 frames) — obstacle
  1. "Ja klar, mein Bike kannst du haben aber wehe du legst den Schwamm..."
  2. "Hmmm, noch 5g Protein heute essen und 3g Kreatin..."
- **Simi** (cx:570, Platform 3): sits on chair, smoking — "Psychoanalyse ist die einzige richtige Antwort." — not obstacle
- **Simon** (cx:1490, Platform 7): bubble to LEFT with tailRight — "Schau mich nicht so an sonst lasse ich dich feuern." — obstacle
- **Corinna** (cx:1555, Platform 7): "Hofi wo ist meine Spezi?" — NOT deadly; S key while touching = launches her upward + notification "Du hast Corinna eine Spezi gegeben."
- Signs: "Innsbruck", "Höttinger Alm", "Umbrüggler Alm"
- Background: Schloss Ambras silhouette, mountain shapes
- Alpine palette

### Level 4 – "Ligurien" (Hardest — Boss Level)
- Lukas spawns at startY:240, falls from shuttle bus at start
- 4 platforms with 80px gaps; lush trees throughout
- **Katha** (cx:470, Platform 2): thought bubble — "5 Prellungen, alles voller blauer Flecken, aber kein Problem, ich bin die nächsten 3 Wochen eh komplett verplant..." — not obstacle
- **Simon** on bar stool (cx:710): "Mein Bike ist schon wieder kaputt, Tre Campari Spritz, per favore." — obstacle
- **Campari Spritz glass** (cx:745) — separate obstacle
- Background: **Johannes** flies past periodically: "Ich will mehr Pizza"
- **GYMMOE** boss at x:1100 — very wide torso, curly hair, lazy speech bubbles
- Walking into GYMMOE = defeat → hint screen
- **P** = Protektor → dialog "Glückwunsch, du hast jetzt einen Protector!" → GYMMOE launches upward
- After boss gone: goal flag at x:1400, walk to it → Win

### Defeat Hint Screen (vs GYMMOE)
> "Wenn du gewinnen willst, musst du den Anfangsbuchstaben deines Geschenks drücken.
> Kannst du es erraten? Es gibt dir eine breitere Brust um den Gymmoe abzudrängen."

### Win Screen
Big banner + confetti. Names list:
> Corinna, Jakob, Johanna, Johannes, Katha, Katy, Felix, Simon, Simon, Simi, Moe, Mona, Niko, Stephanie

Footer: "viel Spaß mit deinem neuen Protector!"
Neustart-Button (click or Space) → returns to title.

---

## Architecture

Single `<script>` block:
```
CONFIG      – W=800, H=450, GRAVITY=0.5, JUMP_FORCE=-12, PLAYER_SPEED=4, PW=28, PH=42
LEVELS      – array: platforms, decos, enemies, events, children, npcs, goalX, worldW, hasBoss
INPUT       – keydown/keyup → keys map + justPressed set; frame counter (let frame=0)
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
- **Kickflip**: K in air → `flipAngle += 0.18` per frame; body rises (`bodyLift`), wheel coin-flips; vy boost + 2× vx (clears 370px gap)
- **GYMMOE bounds**: full canvas height — can't jump over
- **GYMMOE/Corinna pushed**: `launchVy -= 0.4` per frame → accelerates upward; disappears off top
- **NPC system**: `npcs` array `{cx, type, obstacle, gy}`; copied fresh in `loadLevel` via spread; Corinna gets extra state `{pushed, launchVy, y}`
- **drawBubble(bx, by, text, speaker, tailRight=false)**: 5th param flips tail to bottom-right of bubble
- **Felix alternating bubble**: `Math.floor(frame/300)%2` — switches every ~5s
- **Corinna Spezi mechanic**: `keys['KeyS']` (held) while overlapping → launches her; notification shown top-left while pushed
- **Thought bubble**: `drawThoughtBubble(bx,by,text)` — cloud shape + 3 dot chain
- **Children (Level 1)**: drawn via `children` array; Stephie warning on first Helena/Clara hit (includes jump hint)
- **Title screen**: only shows "Pfeiltasten = fahren" — no jump/kickflip spoilers
- **Win condition (Level 4)**: `bossDefeated && player.x >= 1400`
- **P key**: sets `player.prot = true`, shows dialog once, GYMMOE phase → 'pushed'

### Sprite functions
`drawLukas`, `drawEnemy`, `drawGymmoe`, `drawNikoPeek`, `drawStephiePeek`, `drawChild`, `drawMona`, `drawKaty`, `drawJohannes`, `drawNikoStanding`, `drawJohanna`, `drawVelomobil`, `drawSimon`, `drawFelix`, `drawSimi`, `drawCorinna`, `drawKatha`, `drawCampariGlass`, `drawBubble`, `drawThoughtBubble`, `drawDeco`, `drawFlag`, `roundRect`

### Deco types
`bush`, `stone`, `tree`, `bldg`, `frauenkirche`, `olympiaturm`, `samerberg`, `johannisburg`, `ambras`, `mountain`, `sign`, `palm`, `ocean`, `shuttle`

---

## Status
- [x] All 4 levels fully implemented and polished
- [x] All NPCs: Helena, Clara, Mona, Katy, Stephie, Niko, Johanna, Jakob/Velomobil, Felix, Simi, Simon (L3), Corinna, Katha, Simon (L4), Campari, Johannes, GYMMOE
- [x] Bubble positioning fixed (names not obscured), tailRight support added
- [x] Corinna Spezi mechanic (S key), Protector P-key dialog, Neustart button
- [x] Live on GitHub Pages

## GitHub
Repo: `https://github.com/SaltyMoe/Hofigame` — branch `main`
Live: `https://saltymoe.github.io/Hofigame/`
