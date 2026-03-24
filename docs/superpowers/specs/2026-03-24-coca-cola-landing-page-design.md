# Coca-Cola "One in a Million" Landing Page — Technical Design Spec

## Overview

Single-file competition landing page (`index.html`) for a Coca-Cola Gen Z campaign. A **premium, immersive, scroll-driven experience** with cinematic animations, a hero Coke can image with parallax and mouse-follow effects, smooth scroll, ambient particles, custom cursor, and a single-field email entry form with full GA4 analytics. Deploys to Netlify via GitHub with zero build step.

The full copy, layout, design system, and analytics spec are defined in the PRD. This document covers **technical implementation decisions** only.

## Architecture

**Single `index.html`** containing all HTML, CSS, and JavaScript inline. No build tools, no framework, no bundler.

### External Dependencies (CDN only)
- **Lenis** (smooth scroll): `https://cdn.jsdelivr.net/npm/lenis@1.1.18/dist/lenis.min.js` + CSS `https://cdn.jsdelivr.net/npm/lenis@1.1.18/dist/lenis.min.css`
- **GSAP** (animation engine): `https://cdn.jsdelivr.net/npm/gsap@3.12.7/dist/gsap.min.js` + ScrollTrigger plugin `https://cdn.jsdelivr.net/npm/gsap@3.12.7/dist/ScrollTrigger.min.js`
- **Google Fonts Inter**: `https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap`

### Responsive Breakpoint
- Single breakpoint: `@media (min-width: 768px)` — below is mobile, above is desktop

### Configurable Variables (top of `<script>`)
```js
const FORM_ENDPOINT = 'YOUR_FORM_ENDPOINT_HERE';
const GA_MEASUREMENT_ID = 'YOUR_GA4_ID_HERE';
const COMPETITION_NAME = 'One in a Million';
const ELIGIBLE_TERRITORIES = '[eligible territories]';
const DRAW_FREQUENCY = 'monthly';
const CAN_IMAGE_PATH = './assets/coke-can.png';
```

## Sections (7 total, ordered)

1. **Age Gate Modal** — Cinematic full-screen overlay with fade+scale dismiss animation, sessionStorage persistence
2. **Hero** — 100vh, Coke can image with parallax + mouse-follow + scroll-driven rise, character-split headline animation, ambient particles, CTA, sound toggle, scroll prompt
3. **Prize Reveal** — Scroll-triggered staggered prize items with scale+glow entrance, £500 counter animation
4. **The Feeling** — Multi-layer parallax section with animated light streaks, glowing text
5. **How to Enter** — 3-column cards (desktop), stacked (mobile), slide-up entrance
6. **Entry Form** — Spotlight glow activation on scroll, animated gradient border on focus, shimmer CTA
7. **Footer** — Legal text, minimal links

---

## Premium Feature: Lenis Smooth Scroll

- Initialize Lenis on page load for buttery smooth scrolling across the entire page
- Integrate with GSAP ScrollTrigger via `lenis.on('scroll', ScrollTrigger.update)` and `gsap.ticker.add((time) => lenis.raf(time * 1000))`
- Disable Lenis `gsap.ticker.lagSmoothing(0)` for frame-perfect sync
- All scroll-linked animations run through GSAP ScrollTrigger, which Lenis drives

## Premium Feature: GSAP ScrollTrigger Animations

Replaces the basic IntersectionObserver approach. GSAP ScrollTrigger provides:
- Precise scroll-position-linked animations (not just "in view / out of view")
- Scrub animations that progress proportionally to scroll position
- Pin sections during scroll for cinematic reveals
- Stagger animations with easing curves

## Hero Section — Coke Can Image

### Image Setup
- **Source:** `./assets/coke-can.png` — the user-provided Coca-Cola Classic can image (red can with condensation droplets)
- Render as an `<img>` element, centered in the hero, approximately 300px wide on desktop / 200px on mobile
- Apply `filter: drop-shadow(0 0 80px rgba(232,0,45,0.4))` for a red ambient glow
- The can is the visual centrepiece — everything else layers around it

### Mouse-Follow Effect
- Track `mousemove` on the hero section
- Apply a subtle 3D tilt to the can using CSS `transform: perspective(1000px) rotateY(Xdeg) rotateX(Ydeg)`
- Maximum tilt: 8 degrees in any direction
- Use `requestAnimationFrame` with lerp (linear interpolation, factor 0.08) for smooth easing — the can follows the cursor with a slight delay
- On mobile: disable mouse-follow (no hover), use subtle CSS float animation instead

### Scroll-Driven Can Animation (GSAP ScrollTrigger)
- As user scrolls from 0 to 100vh:
  - Can translates upward (`y: -150px`)
  - Can scales up slightly (`scale: 1.1`)
  - Can rotates subtly on Z-axis (`rotate: -5deg`)
  - Red glow intensifies (`drop-shadow` radius increases)
- `scrub: 1` for smooth scroll-linked progression

### Bubble/Fizz Particle System
- **Canvas-based** (separate 2D canvas layered behind the can, above the background)
- 60-80 small circles (white, 1-4px radius, 0.3-0.8 opacity)
- Particles rise from the can top at varying speeds (0.5-2px per frame)
- Slight horizontal drift (sine wave, ±15px amplitude)
- Particles fade out as they reach upper viewport
- Activated when scroll > 50px (GSAP ScrollTrigger)
- Render loop tied to `requestAnimationFrame`, paused when hero is not visible

## Premium Feature: Cinematic Text Animations

### Character-Split Headline ("One in a Million.")
- On page load (after age gate dismisses), the H1 headline animates in:
  1. Each character is wrapped in a `<span>` via JS
  2. GSAP `from` animation: `{ opacity: 0, y: 60, rotateX: -90 }` → visible
  3. Stagger: `0.03s` per character, ease: `power4.out`, duration: `1.2s`
  4. Total animation time: ~1.5s for the full headline

### Shimmer/Glow Sweep on Headline
- After the character animation completes, a **shimmer sweep** plays:
- CSS pseudo-element with `linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent)`
- Animated via `background-position` from left to right over 1.5s
- Plays once, then subtle repeat every 8s

### Section Headlines (H2s)
- Each H2 uses a **clip-reveal** animation:
  - Text is wrapped in a container with `overflow: hidden`
  - Text starts at `translateY(100%)` and animates to `translateY(0)`
  - Triggered by ScrollTrigger when section enters viewport
  - Duration: 0.8s, ease: `power3.out`

### Section Labels ("What you win", "How to enter")
- Fade in + `letterSpacing` animation: starts at `0.3em`, animates to `0.15em`
- Creates an expanding-then-settling effect

## Premium Feature: Ambient Particle Background

- **Full-page canvas** (position: fixed, z-index: 1, pointer-events: none)
- 80-120 tiny particles (white dots, 1-2px, opacity 0.1-0.3)
- Particles drift slowly in random directions (0.1-0.3px per frame)
- Subtle parallax: particles shift slightly based on scroll position (creates depth)
- Very low opacity — atmospheric, not distracting
- Canvas resizes on window resize
- Animation loop paused when tab is not visible (`document.hidden`)

## Premium Feature: Custom Cursor

- **Default state:** 8px circle, `#E8002D` fill, with a faint 20px trailing glow (using a second, larger circle that follows with delay)
- **Hover state (interactive elements):** outer circle expands to 48px, becomes a ring (border only), inner dot stays 8px
- **CTA hover:** outer ring expands to 64px, text "ENTER" appears inside (12px, white, uppercase)
- Implementation: two `div` elements (`.cursor-dot`, `.cursor-ring`), positioned via `transform: translate()`, updated on `mousemove` with RAF
- `.cursor-dot` follows immediately, `.cursor-ring` follows with lerp (factor 0.15)
- Hide on mobile (touch devices): check `window.matchMedia('(hover: hover)')`
- Set `* { cursor: none; }` on desktop to hide native cursor
- Restore native cursor on form inputs (`.email-input { cursor: text; }`)

## Premium Feature: Magnetic Buttons

- All CTA buttons have a **magnetic hover effect**:
  - On `mousemove` within a 100px radius of the button center, the button translates toward the cursor
  - Maximum displacement: 12px in any direction
  - Lerp factor: 0.2 for smooth magnetic pull
  - On `mouseleave`: animate back to origin with `gsap.to(btn, { x: 0, y: 0, duration: 0.4, ease: 'elastic.out(1, 0.5)' })`
- **Ripple effect on click:**
  - On click, create a `<span>` at click position inside the button
  - Animate: `scale(0)` → `scale(4)`, opacity `0.4` → `0`, duration 0.6s
  - Span has `background: rgba(255,255,255,0.3)`, `border-radius: 50%`
  - Remove span from DOM after animation

## Prize Reveal Section — Upgraded

### Prize Items Entrance
- Each item animates in via GSAP ScrollTrigger:
  - `from: { opacity: 0, y: 40, scale: 0.95 }` → visible
  - Stagger: `0.15s` between items
  - On arrival: a brief **flash/pulse** on the red accent bar (opacity `1` → `0.5` → `1` over 0.3s)

### Red Accent Bar Glow
- Accent bars have a subtle pulsing glow: `box-shadow: 0 0 12px rgba(232,0,45,0.5)`
- Glow pulses via CSS animation: opacity oscillates between 0.3 and 0.7, duration 3s, infinite

### £500 Counter Animation
- The "£500" text in the prize item uses a **number counter roll-up**:
  - When the item scrolls into view, animate from `£0` to `£500` over 1.5s
  - Use GSAP `to` with an object `{ val: 0 }` → `{ val: 500 }`, `onUpdate` sets innerHTML
  - Easing: `power2.out` (fast start, slow finish)
  - Format with no decimals

## The Feeling Section — Multi-Layer Parallax

### Layer Structure (back to front)
1. **Background gradient**: dark warm tones (`#1a0a00` to `#0a0505`)
2. **Bokeh layer**: CSS-generated bokeh circles (8-12 circles, varying sizes 30-120px, warm amber/gold, `border-radius: 50%`, `filter: blur(30px)`, opacity 0.15-0.3). These translate at a slower scroll rate (parallax factor 0.3)
3. **Light streaks**: 3-4 diagonal CSS gradient lines (`linear-gradient`) that slowly animate position (CSS keyframe, 15s duration, infinite)
4. **Text layer**: headlines and body copy (parallax factor 1.0 — normal scroll speed)

### Text Glow
- The H2 headline has `text-shadow: 0 0 40px rgba(232,0,45,0.2), 0 0 80px rgba(232,0,45,0.1)`
- Glow subtly pulses: opacity oscillates via CSS animation, 4s cycle

### Parallax Implementation
- GSAP ScrollTrigger with `scrub: true`
- Each layer has a different `y` translation range, creating depth as user scrolls

## Entry Form Section — Spotlight Effect

### Spotlight Activation
- Background starts as flat dark (`#0A0A0A`)
- When section scrolls into view (ScrollTrigger), the radial gradient center **animates from 0% opacity to full**:
  - `radial-gradient(ellipse at center, rgba(26,0,0,0) 0%, ...)` → `radial-gradient(ellipse at center, #1A0000 0%, ...)`
  - Duration: 1.2s, ease: `power2.inOut`
- Creates the effect of a spotlight turning on to illuminate the form

### Animated Gradient Border on Focus
- On input focus, the border transitions from static `rgba(255,255,255,0.15)` to an **animated gradient sweep**:
  - Use a wrapper `div` with `background: conic-gradient(from var(--angle), #E8002D, rgba(255,255,255,0.15), #E8002D)`
  - Animate `--angle` from `0deg` to `360deg` via `@property` and CSS animation (2s, linear, infinite)
  - Input sits inside with 1.5px inset, creating the glowing border effect
  - On blur: fade back to static border

### Submit Button Shimmer
- A subtle **shine sweep** animation loops on the submit button:
  - CSS pseudo-element: `linear-gradient(110deg, transparent 30%, rgba(255,255,255,0.15) 50%, transparent 70%)`
  - Animated: `background-position` from `-200%` to `200%`, duration 3s, infinite
  - Very subtle — catches the eye without being distracting

## Age Gate — Cinematic Dismiss

- On "Yes, I'm in" click:
  1. Button scales up briefly (`scale: 1.05`, 0.1s)
  2. Modal card fades and scales: `opacity: 1, scale: 1` → `opacity: 0, scale: 0.9`, duration 0.4s, ease `power2.inOut`
  3. Backdrop fades: `opacity: 1` → `opacity: 0`, duration 0.6s (starts 0.2s after card)
  4. After animation completes: remove modal from DOM, trigger hero entrance animations
  5. Set `sessionStorage.setItem('ageVerified', 'true')`
- If already verified (sessionStorage): skip modal entirely, trigger hero animations immediately
- "Not yet" → same card animation but redirects to `about:blank` after animation completes

## Scroll Prompt — Enhanced

- Pulsing chevron arrow (SVG, not text character)
- Animation: `translateY(0)` → `translateY(8px)` → `translateY(0)`, 2s ease-in-out, infinite
- Opacity breathing: 0.3 → 0.6 → 0.3 in sync
- Fades out as user starts scrolling (GSAP ScrollTrigger, first 100px of scroll → opacity 0)

## Sound Toggle

- Web Audio API: `OscillatorNode` generating a low ambient drone (sine wave, ~80Hz, very low gain) layered with filtered noise for atmosphere
- Default: muted
- Toggle button: top-right hero, inline SVG speaker icon
- State persisted in `sessionStorage` (key: `soundEnabled`)

## Form Handling

1. Client-side email validation via regex + `input.validity`
2. Invalid state: red border, inline error "Please enter a valid email address"
3. Submit: `fetch(FORM_ENDPOINT, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify({ email }) })`
4. Success: HTTP 200 or 201 → form replaced with confirmation message ("You're in." etc.) with scale-up entrance animation
5. Error (non-2xx or network failure): show "Something went wrong. Please try again." — email value preserved
6. Prevent double-submit: disable button on click, re-enable on error
7. **Dev mode fallback:** if `FORM_ENDPOINT` equals `'YOUR_FORM_ENDPOINT_HERE'`, log payload to console and show success state

## Analytics (GA4 + dataLayer)

All events wrapped in `if (typeof gtag !== 'undefined')` check. Parallel `dataLayer.push()` for GTM.

Events implemented per PRD Section 9.2:
- `age_gate_passed`, `age_gate_failed`
- `sound_enabled`
- `scroll_25`, `scroll_50`, `scroll_75`, `scroll_100` — fire once per page load, calculated as `scrollY / (document.body.scrollHeight - window.innerHeight)`, tracked via throttled scroll listener (100ms `requestAnimationFrame` gate)
- `prize_section_viewed`
- `cta_hero_clicked`, `cta_prize_clicked`
- `form_field_focused`
- `form_abandoned` — fires on `beforeunload` if email field has been focused but form was not submitted
- `competition_entry_submitted`, `entry_confirmation_viewed`
- `time_on_page_30s`, `time_on_page_60s`, `time_on_page_120s`
- `bottle_interaction` — fires once when user scrolls past hero section (triggering the can animation)

## Performance Targets

- Lighthouse mobile: >= 65 (adjusted for GSAP + Lenis + particle systems)
- Particle canvas: single `requestAnimationFrame` loop for both ambient + bubble particles
- Festival background: lazy-loaded via IntersectionObserver
- Can image: preloaded via `<link rel="preload">` (it's above the fold)
- `will-change: transform` on animated elements only during animation, removed via GSAP `onComplete`
- Pause all canvas animations when `document.hidden` is true (tab not visible)
- Total JS payload (CDN): ~80KB gzipped (GSAP ~30KB + ScrollTrigger ~12KB + Lenis ~8KB + inline JS)

## Accessibility

- All interactive elements: `aria-label` attributes
- Form field: associated `<label>` element
- Age gate: keyboard navigable, focus trap
- CTA buttons: sufficient color contrast (white on `#E8002D` = ~4.8:1 AA pass)
- Scroll prompt and sound toggle: labeled for screen readers
- Custom cursor: hidden on touch devices, native cursor restored on form inputs
- **`prefers-reduced-motion: reduce`:** When matched:
  - Lenis smooth scroll disabled (native scroll)
  - All GSAP animations set to `duration: 0`
  - Character split animations instant
  - Particle systems disabled
  - Mouse-follow and magnetic effects disabled
  - Can is static (no scroll animation)
  - Custom cursor simplified (no trailing ring)

## Design System Reference

All colors, typography, spacing, and component specs follow the PRD Sections 6.1–6.5 exactly. No deviations.

## Testing Strategy

- **Manual browser testing:** Chrome, Firefox, Safari, Edge at 375px and 1440px
- **Form validation:** test empty, invalid, valid email states
- **Age gate:** both paths, session persistence, reload, cinematic dismiss animation
- **Scroll animations:** verify all GSAP ScrollTrigger animations fire and scrub correctly
- **Particle systems:** confirm ambient + bubble canvases render, pause when hidden
- **Custom cursor:** verify hide on mobile, expand on hover, text on CTA hover
- **Magnetic buttons:** test hover pull, elastic return, ripple click
- **Smooth scroll:** verify Lenis initializes, syncs with GSAP
- **Analytics:** verify events in browser console (dataLayer inspection)
- **Performance:** check Lighthouse, verify no jank at 60fps
- **Accessibility:** keyboard navigation, reduced motion mode
- **Reduced motion:** toggle `prefers-reduced-motion` in devtools, verify all animations disabled

## File Structure

```
/
├── index.html          # Everything — HTML, CSS, JS
├── assets/
│   └── coke-can.png    # Coca-Cola Classic can image (user-provided)
└── docs/
    └── superpowers/
        └── specs/
            └── 2026-03-24-coca-cola-landing-page-design.md
```
