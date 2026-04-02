# Ryan's Yard — Claude Code Handoff

This is a browser-based birthday gift game for Ryan, turning 14. Four sports mini-games played in sequence, ending on a personalized score screen. Read this entire file before writing any code.

---

## What We're Building

**Ryan's Yard** — a mobile-first single-page React game. No backend. No router. No external assets. Deployed to Vercel as a shareable URL.

- Stack: React 18 + TypeScript (strict) + Vite + Tailwind CSS v3
- Font: Press Start 2P via Google Fonts (add to index.html `<head>`)
- Colors: Jets green `#203731`, white `#ffffff`, black `#000000`
- Orientation: Landscape only (CSS media query enforced)
- Jersey number `#14` appears as a decorative element throughout

---

## Screen Flow

```
title → round-intro → baseball
      → round-intro → basketball
      → round-intro → football
      → round-intro → lacrosse
      → score → title (play again)
```

---

## App State (App.tsx owns everything)

```typescript
type Screen =
  | 'title'
  | 'round-intro'
  | 'baseball'
  | 'basketball'
  | 'football'
  | 'lacrosse'
  | 'score';

interface AppState {
  screen: Screen;
  roundIndex: number; // 0–3
  scores: [number, number, number, number];
}
```

- All state lives in `App.tsx` via `useState`
- Each game component receives `onComplete: (score: number) => void`
- Game loop state (positions, timers, RAF handles) lives in `useRef`, never `useState`
- Always clean up `requestAnimationFrame` in `useEffect` return

---

## CSS Variables (define in index.css)

```css
:root {
  --jets-green: #203731;
  --jets-green-light: #2d4f44;
  --white: #ffffff;
  --black: #000000;
  --dirt: #8B6914;
  --asphalt: #3a3a3a;
  --grass: #2d6a1f;
  --sky: #87CEEB;
  --accent-yellow: #FFD700;
}
```

Never hardcode hex values in components — always use these variables.

---

## Orientation Lock (index.css)

```css
@media (orientation: portrait) {
  .game-root { display: none; }
  .rotate-prompt { display: flex; }
}
@media (orientation: landscape) {
  .game-root { display: flex; }
  .rotate-prompt { display: none; }
}
```

---

## Tailwind Config

Update `tailwind.config.js` content array:

```js
content: ["./index.html", "./src/**/*.{ts,tsx}"],
```

---

## Game Specs

### Baseball ⚾
- Setting: Schoolyard diamond, CSS-drawn
- Mechanic: Pitch flies top→bottom, player taps SWING in timing window
- 3 strikes ends round. Hit = +10pts. Consecutive hit streak = +5 bonus each.
- Difficulty: pitch speed increases every 2 hits, hit window narrows
- SWING button: fixed bottom-center, min 80×80px
- Use `requestAnimationFrame` for ball animation via `useRef`

### Basketball 🏀
- Setting: Driveway with garage hoop on the right
- Mechanic: Vertical shot meter fills/empties in loop. Tap SHOOT in green zone.
- 10 attempts. Green zone = +10pts. Center of green zone = +15pts.
- Difficulty: green zone shrinks 2% per basket, meter speed increases every 3
- SHOOT button: fixed bottom-center, min 80×80px

### Football 🏈
- Setting: Backyard with goalposts at far end
- Mechanic: Receiver runs route L→R. Tap THROW — ball goes to receiver's position at tap time.
- 8 throws. Completion = +10pts. Clean completion (no defenders nearby) = +15pts.
- Difficulty: receiver speed up after 3 completions, 2nd defender added after 4
- Collision detection: defender within 30px of ball endpoint = interception
- THROW button: fixed bottom-center, min 80×80px

### Lacrosse Goalie 🥍
- Setting: Park field, goal centered, goalie POV
- Mechanic: Shot incoming from L/C/R. Indicator flashes zone. Tap correct zone to save.
- 10 shots. Save = +10pts. Save on fast shot (top-third difficulty) = +15pts.
- Difficulty: indicator display time shortens, shot speed increases each save
- Three tap zones: Left / Center / Right, each min 33vw, cover bottom half of screen

---

## Score Screen Messages

| Total | Message |
|---|---|
| 0–99 | "You gave it a shot, Ry. The yard will be there tomorrow. 🏡" |
| 100–199 | "Not bad for a birthday kid. The yard respects the effort. 💪" |
| 200–299 | "Solid run, #14. Ryan's Yard has been conquered. 🏆" |
| 300–349 | "LEGENDARY. Ryan's Yard bows down to its creator. 👑" |
| 350+ | "PERFECT GAME. The yard is YOURS, #14. Happy Birthday! 🎉" |

---

## Build Order (suggested)

1. `index.css` — CSS variables, Tailwind directives, orientation lock, font import
2. `index.html` — add Press Start 2P Google Fonts link
3. `types.ts` — all shared TypeScript types
4. `App.tsx` — state machine and screen router
5. `RotatePrompt.tsx` — portrait warning screen
6. `TitleScreen.tsx` — tap-to-play start screen
7. `RoundIntro.tsx` — round transition card
8. `Baseball.tsx` — game 1
9. `Basketball.tsx` — game 2
10. `Football.tsx` — game 3
11. `Lacrosse.tsx` — game 4
12. `ScoreScreen.tsx` — final results

---

## Hard Rules

- TypeScript strict mode. No `any`. No `@ts-ignore` without explanation.
- No external assets. No animation libraries. No router. No sound. No server.
- No hover states. No placeholders. No TODOs in delivered code.
- Touch targets min 48×48px everywhere.
