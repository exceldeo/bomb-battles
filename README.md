# 💣 Bomb Battle!

> A 2-player turn-based browser game — no install, no server, just open and play!

![HTML](https://img.shields.io/badge/HTML-E34F26?style=flat&logo=html5&logoColor=white)
![CSS](https://img.shields.io/badge/CSS-1572B6?style=flat&logo=css3&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=javascript&logoColor=black)
![No Dependencies](https://img.shields.io/badge/dependencies-none-brightgreen?style=flat)

---

## 📖 Overview

Bomb Battle is a fun two-player turn-based game that runs entirely in a **single HTML file**. Two players take turns flipping boxes on a shared grid, trying to collect as many safe boxes as possible without triggering a hidden bomb. The player who hits the bomb **loses**!

The game features a hand-drawn cartoon toy aesthetic — chunky ink borders, warm parchment colors, bouncy animations, and playful typography powered by **Fredoka One** font.

---

## ⚡ Quick Start

1. **Download** `bomb-battle.html`
2. **Open** it in any modern web browser (Chrome, Firefox, Safari, Edge)
3. **Configure** grid size and number of bombs using the arrow sliders
4. **Click** "Let's Play!" and share the screen with your opponent

> No internet required after first load. The only external resource is the Google Fonts stylesheet.

---

## 📜 Game Rules

### Players & Turns

- There are exactly **2 players** per game
- Players alternate turns — Player 1 🔴 always goes first
- Each turn a player may choose **one or more boxes** by clicking them
- Clicking a chosen box again **deselects** it
- Press **"End Turn!"** to confirm all chosen boxes and pass play to the opponent

### Opening Boxes

- All chosen boxes are revealed simultaneously when the turn ends
- Safe boxes show the player's icon (❤️ for P1, 💙 for P2) with a countdown pip
- Boxes stay open for **2 of the owner's turns**, then close again
- A small number pip in the corner shows how many owner-turns remain: **2** = bright, **1** = dim

### Bomb Rule

- One or more bombs are hidden randomly in the grid at game start
- If **any** of a player's chosen boxes contains a bomb, that player **loses instantly**
- The bomb is revealed with an explosion 💥 effect and the winner is declared

### Scoring

- Each safe box successfully opened counts as **+1 point** for that player
- Score is displayed in the HUD throughout the game
- The game ends when a bomb is triggered — the player who did **not** hit the bomb wins

---

## 👁️ Box Visibility Rule (Key Mechanic)

Boxes remain open for **exactly 2 of the owner's turns**. This creates a memory challenge — both players can see recently opened boxes, but old boxes close and must be re-discovered.

| Turn   | Player | Action                | Box State After Turn                                  |
| ------ | ------ | --------------------- | ----------------------------------------------------- |
| P1 — 1 | 🔴 P1  | Opens box 1           | Box 1 open (pip: **2**)                               |
| P2 — 1 | 🔵 P2  | Opens box 4           | Boxes 1, 4 open                                       |
| P1 — 2 | 🔴 P1  | Opens box 2           | Box 1 dims (pip: **1**), box 2 open (pip: **2**)      |
| P2 — 2 | 🔵 P2  | Opens box 5           | Boxes 2, 4 dim, 5 open                                |
| P1 — 3 | 🔴 P1  | Opens box 3           | Box 1 **closes!** Box 2 dims, box 3 open (pip: **2**) |
| P1 — 4 | 🔴 P1  | Can pick box 1 again! | Box 1 is free and clickable again ✅                  |

**Rule summary:**

- Opened on owner-turn **T** → dims on owner-turn **T+1** → closes at end of owner-turn **T+2** → free from owner-turn **T+3**

---

## ⚙️ Configuration Options

| Setting         | Options         | Default | Notes                    |
| --------------- | --------------- | ------- | ------------------------ |
| Grid Size       | 4×4 / 5×5 / 6×6 | 5×5     | Use arrows ◀ ▶ to change |
| Number of Bombs | 1 / 2 / 3       | 1       | More bombs = more chaos! |

---

## 🖥️ Interface Guide

### HUD (Top Bar)

- **Player panels** — Shows each player's score and turn status. The active player's panel lifts and glows
- **Turn badge** (centre) — Shows whose turn it is and how many boxes are currently selected

### Grid Cell States

| Cell Appearance           | Meaning                                        |
| ------------------------- | ---------------------------------------------- |
| ⬜ White box              | Closed — safe to pick                          |
| 🟨 Yellow ✅              | Selected this turn (pending)                   |
| 🟥 Pink ❤️ bright (pip 2) | Opened by P1, 2 owner-turns until close        |
| 🟦 Pink ❤️ dim (pip 1)    | Opened by P1, closes at end of this owner-turn |
| 🟦 Blue 💙 bright/dim     | Same as above but for P2                       |
| 💣 Explosion              | Bomb hit — game over!                          |

### Controls

- **Click a box** — Select / deselect it for this turn
- **"End Turn!" button** — Confirm selections, reveal boxes, pass to opponent
- **Activity log** (bottom strip) — Running history of all moves

---

## 🛠️ Technical Details

### File Structure

```
bomb-battle.html
├── <style>     Fredoka One font, CSS variables, all animations
├── <body>      Setup screen + game screen HTML structure
└── <script>    Game engine — state, turn logic, rendering
```

### Key Variables

| Variable           | Type   | Description                                              |
| ------------------ | ------ | -------------------------------------------------------- |
| `openedCells`      | Map    | `cellIndex → { player, openedAt }` — all open boxes      |
| `playerOwnTurn[p]` | Number | Increments each time player `p` ends a turn              |
| `bombs`            | Set    | Cell indices containing bombs (randomised at game start) |
| `pending`          | Array  | Cell indices selected in the current turn                |

### Visibility Logic

A cell opened when `playerOwnTurn[p] = T` has:

```
turnsLeft = 2 - (playerOwnTurn[p] - openedAt)
```

- `turnsLeft === 2` → **bright**, while `playerOwnTurn[p] <= T+1`
- `turnsLeft === 1` → **dim**, when `playerOwnTurn[p] === T+1`
- `turnsLeft === 0` → **closes** at end of `playerOwnTurn[p] === T+2`

Closing is triggered inside `endTurn()` after incrementing `playerOwnTurn`, so the cell is immediately free for the next turn.

### Browser Compatibility

| Browser         | Version | Status             |
| --------------- | ------- | ------------------ |
| Chrome / Edge   | 90+     | ✅ Fully supported |
| Firefox         | 88+     | ✅ Fully supported |
| Safari          | 14+     | ✅ Fully supported |
| Mobile browsers | Modern  | ✅ Touch supported |

---

## 🎨 Customisation

All visual settings are centralised in CSS variables at the top of the `<style>` block:

```css
:root {
  --p1: #e8473f; /* Player 1 red         */
  --p1light: #ffd6d4; /* Player 1 light fill  */
  --p1dark: #b52e27; /* Player 1 dark accent */
  --p2: #2f7fc1; /* Player 2 blue        */
  --p2light: #cce4f7; /* Player 2 light fill  */
  --p2dark: #1a5a8f; /* Player 2 dark accent */
  --bg: #fdf6e3; /* Page background      */
  --ink: #3a2a1a; /* Border / text color  */
  --yellow: #f7c948; /* Accent / buttons     */
  --green: #4caf50; /* Start button         */
}
```

To change the maximum allowed bombs or available grid sizes, edit the `gridOptions` array and the `Math.max/min` guards in `changeBombs()` inside the `<script>` block.

---

## ✨ Credits

| Item       | Detail                                                                                                                             |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Font       | [Fredoka One](https://fonts.google.com/specimen/Fredoka+One) + [Nunito](https://fonts.google.com/specimen/Nunito) via Google Fonts |
| Engine     | Pure Vanilla JS — zero dependencies                                                                                                |
| Design     | Hand-drawn cartoon toy aesthetic — chunky ink borders, warm parchment palette                                                      |
| Built with | [Claude](https://claude.ai) (Anthropic)                                                                                            |

---

_Have fun — and watch out for the bombs! 💣_
