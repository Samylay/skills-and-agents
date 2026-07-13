---
name: interaction-craft
description: Emil Kowalski's web animation and interaction doctrine (animations.dev, Sonner, Vaul) — a house style for how your apps should feel. Use whenever building or modifying UI in any app: components, pages, buttons, modals, drawers, toasts, transitions, hover/press states, loading states, or when the user mentions animations, motion, tactility, haptics, or "feel".
---

# Interaction Craft

House doctrine for making UI *feel* good, from Emil Kowalski (Linear design engineer, author of Sonner/Vaul/animations.dev). Apply this to ALL UI work by default — new components inherit these rules without being asked.

> **Canonical source:** Emil Kowalski's paid course "Animations on the Web" — https://animations.dev. This skill is a distillation; when a question isn't answered here, the course is the reference of record. Do not rebuild or re-derive the doctrine from scratch.

## The frequency rule (decide first: should this animate?)

- **Rare** (onboarding, empty states, completions, marketing/portfolio): delight allowed — morphing, springs, staggered reveals.
- **Daily** (dashboards, forms, navigation): subtle and fast, 150–250ms.
- **Constant** (list check-offs done dozens of times, keyboard-driven actions, command palettes): **no animation**. Instant is the design.
- A good default preference: apps should feel *nice*, not austere — lean animated on daily surfaces, but keep keyboard/constant actions instant.

## Numbers and curves

- Everything ≤300ms (drawers/page-level up to 500ms). 180ms beats 400ms.
- Never default CSS `ease`/`linear` for UI motion. Standard tokens:
  ```css
  --ease-out-custom: cubic-bezier(0.25, 1, 0.5, 1);    /* entering elements */
  --ease-in-out-custom: cubic-bezier(0.45, 0, 0.15, 1); /* elements moving on screen */
  --ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);        /* iOS feel; drawers, sheets */
  --dur-fast: 150ms; --dur-base: 200ms; --dur-slow: 300ms;
  ```
- `ease-out` for enters, `ease-in-out` for moves, springs for gesture-driven.

## Hard rules

1. Animate ONLY `transform`, `opacity`, `clip-path`, `filter` — never width/height/padding/margin/top/left. Reveals use `clip-path: inset(...)`; progress bars use `transform: scaleX()`.
2. CSS transitions over keyframes — transitions retarget smoothly when interrupted mid-flight.
3. Press feedback on every button/actionable card: `active:scale-[0.97] transition-transform duration-150` (Tailwind) — the web's haptics.
4. Never enter from `scale(0)` — start at `scale(0.9)` or higher.
5. Origin-aware motion: dropdowns/popovers grow from their trigger (`transform-origin`), never from center.
6. Tooltips: first shows with delay + animation; siblings shown right after appear instantly.
7. `prefers-reduced-motion` respected in every app's global CSS:
   ```css
   @media (prefers-reduced-motion: reduce) {
     *, *::before, *::after { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
   }
   ```
8. Optimistic UI on frequent mutations: state flips instantly, server catches up; no spinner for sub-200ms operations. Skeletons (subtle shimmer) for genuinely slow loads.
9. `transition-all` is banned — transition the specific properties.
10. A touch of `filter: blur(2px) → 0` on enters can mask imperfect state transitions.

## Standard patterns

- **Enter** (dropdowns, toasts, page sections): `opacity 0→1` + `translateY(4px)→0`, 200ms, `--ease-out-custom`. Page sections may stagger ~30ms apart, once per navigation.
- **Hover lift** (cards): `translateY(-2px)` + shadow, 200ms.
- **Celebration** (rare, meaningful events only — lesson complete, goal shipped): SVG stroke draw-in or scale spring, ≤400ms.
- **Count-up numbers** on rarely-viewed stat tiles.
- **Nested menus — safe triangle:** when the cursor moves diagonally toward a submenu it briefly leaves the parent item's bounds; native UIs (macOS, Windows, Amazon) keep the submenu open by tracking the triangle between cursor and submenu corners. Don't hand-roll this: Radix UI dropdown/menubar primitives implement it. Before building any nested menu, check whether the app already uses Radix and build on that.

## Libraries (prefer Emil's own; new deps need the user's OK if unattended)

- **Toasts: Sonner** — never hand-roll notifications. Defaults are pre-tuned.
- **Drawers/sheets (mobile): Vaul** — gesture-driven, velocity-based dismissal, iOS curve.
- **motion (framer-motion)** only when CSS can't do it: springs, `layoutId` shared-element transitions, `whileInView` scroll reveals. CSS covers most of this doctrine.

## Apple-motion review rubric (17 checks)

Emil's distillation of Apple's WWDC design talks (source: https://github.com/emilkowalski/skills, `apple-design`; full text in `references/apple-design.md`). Use as a scoring checklist when reviewing existing UI or shipping gesture-driven work — score each pass/fail, fix fails in priority order 1→17:

1. **Response** — feedback on pointer-*down*, not release; no artificial latency on the input path.
2. **Direct manipulation** — dragged elements track 1:1 and respect the grab offset (no snap-to-center).
3. **Interruptibility** — every animation grabbable/reversible mid-flight; animate from the live presentation value, never the target.
4. **Springs over scripts** — gesture-driven motion uses springs (damping 1.0 default; ~0.8 only after a flick/throw).
5. **Velocity handoff** — release velocity feeds the spring; no seam between drag and animation.
6. **Momentum projection** — flicks land where the gesture was *going* (`(v/1000)·d/(1−d)`, d≈0.998), not nearest-to-release-point.
7. **Spatial consistency** — enter and exit along the same path; popovers originate from their trigger.
8. **Gesture hinting** — intermediate motion telegraphs the outcome.
9. **Rubber-banding** — boundaries resist progressively, never hard-stop.
10. **Gesture details** — ~10px hysteresis, cancel-by-drag-away, parallel gesture detection.
11. **Frame smoothness** — compositor-only properties; no strobing on fast motion.
12. **Materials & depth** — translucent chrome (`backdrop-filter`) encodes hierarchy; never stack light-on-light translucency; dim-to-focus for modal, no scrim for parallel panels.
13. **Multimodal harmony** — visual/sound/haptic fire the same frame, only at meaningful moments.
14. **Reduced motion** — cross-fades replace slides/springs; also honor `prefers-reduced-transparency` and `prefers-contrast`.
15. **Typography** — size-specific tracking (negative on display, ~0 on body), leading inverse to size, rem-based spacing.
16. **Eight foundations** — purpose, agency, responsibility, familiarity, flexibility, simplicity, craft, delight; wayfinding on every screen (where am I / where can I go / how do I get out).
17. **Process** — prototype interactively; review motion frame-by-frame before shipping.

## Verify (greppable predicates for any animation task)

- `grep -rn 'transition-all' src/` → empty.
- No animated `width|height|padding|margin|top|left` properties.
- Durations ≤500ms; `prefers-reduced-motion` block present in globals.
- Interactive elements have press feedback; reduced-motion still leaves the app fully usable.
