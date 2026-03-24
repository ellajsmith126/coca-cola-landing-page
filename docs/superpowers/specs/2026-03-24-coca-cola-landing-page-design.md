# Coca-Cola "One in a Million" Landing Page — Technical Design Spec

## Overview

Single-file competition landing page (`index.html`) for a Coca-Cola Gen Z campaign. Dark, immersive, scroll-driven experience with a 3D bottle hero, age gate, single-field email entry form, and full GA4 analytics. Deploys to Netlify via GitHub with zero build step.

The full copy, layout, design system, and analytics spec are defined in the PRD (provided by the user). This document covers **technical implementation decisions** only.

## Architecture

**Single `index.html`** containing all HTML, CSS, and JavaScript inline. No build tools, no framework, no bundler.

### External Dependencies (CDN only)
- Three.js r162: `https://cdnjs.cloudflare.com/ajax/libs/three.js/r162/three.min.js` (r162 required for `MeshPhysicalMaterial` transmission/thickness support; r128 does not support these properties)
- Google Fonts Inter: `https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap`

### Responsive Breakpoint
- Single breakpoint: `@media (min-width: 768px)` — below is mobile, above is desktop

### Configurable Variables (top of `<script>`)
```js
const FORM_ENDPOINT = 'YOUR_FORM_ENDPOINT_HERE';
const GA_MEASUREMENT_ID = 'YOUR_GA4_ID_HERE';
const COMPETITION_NAME = 'One in a Million';
const ELIGIBLE_TERRITORIES = '[eligible territories]';
const DRAW_FREQUENCY = 'monthly';
```

## Sections (7 total, ordered)

1. **Age Gate Modal** — Full-screen overlay, sessionStorage persistence, redirect on "Not yet"
2. **Hero** — 100vh, 3D bottle canvas, headline, CTA, sound toggle, scroll prompt
3. **Prize Reveal** — Scroll-triggered staggered prize item animations
4. **The Feeling** — Parallax background image section, emotional copy
5. **How to Enter** — 3-column cards (desktop), stacked (mobile)
6. **Entry Form** — Single email field, client-side validation, fetch POST, confirmation state
7. **Footer** — Legal text, minimal links

## 3D Bottle (Three.js)

### Geometry
- **Simplified placeholder bottle** built with `LatheGeometry` using a hand-tuned profile curve that approximates a Coke bottle silhouette (wide body, narrow neck, lip)
- Cap modeled as a simple `CylinderGeometry` on top

### Material
- `MeshPhysicalMaterial` with:
  - `color: #1a0a0a` (dark cola tint)
  - `roughness: 0.1`, `metalness: 0.0`
  - `transmission: 0.85`, `thickness: 0.5` (glass-like transparency)
  - `clearcoat: 1.0`
- Coca-Cola label: red band around bottle body via a second mesh (thin cylinder, `#E8002D`, slightly larger radius)

### Lighting
- Warm amber `PointLight` from below
- Cool blue-white `DirectionalLight` from above
- Subtle `AmbientLight` for fill

### Animation
- Constant slow Y-axis rotation: `0.003 rad/frame`
- On scroll: bottle `position.y` increases proportionally to `window.scrollY`
- Bubble particle system: `Points` with `BufferGeometry` (500 vertices, single draw call) instead of individual meshes — keeps draw calls under budget. White circular point sprites, rising at random speeds, activated when scroll > 100px
- Camera pulls back slightly on scroll (`camera.position.z` increases)

### GLTF Upgrade Path
- Include commented-out `GLTFLoader` code with CDN source: `https://cdn.jsdelivr.net/npm/three@0.162.0/examples/jsm/loaders/GLTFLoader.js`
- When using GLTF, load via `<script type="importmap">` to resolve bare `three` imports, with a `<script type="module">` block for the loader
- Single constant `USE_GLTF_MODEL = false` and `GLTF_MODEL_PATH = './assets/bottle.glb'`
- When ready: set to `true`, add the importmap + module script, drop `.glb` in `/assets/`

### WebGL Fallback
- On init, check `canvas.getContext('webgl')` — if null, hide canvas and show a static CSS gradient + centered Coca-Cola wordmark as the hero background instead

## Scroll Animations

**Engine:** `IntersectionObserver` API (no external libraries)

**Animated elements:**
- Prize items: `translateY(30px) opacity(0)` → `translateY(0) opacity(1)`, 100ms stagger between items
- Section labels: fade in on enter
- Step cards: slide up on enter
- Feeling section headline: fade in

**CSS approach:** Elements start with `.hidden` class (`opacity: 0; transform: translateY(30px)`). Observer adds `.visible` class. All transitions via CSS `transition: transform 0.6s ease, opacity 0.6s ease`.

`will-change: transform` applied to animated elements via the `.hidden` class, removed via `transitionend` event handler after animation completes.

## Age Gate

- Renders as fixed overlay (`z-index: 9999`) before page is interactable
- "Yes, I'm in" → `sessionStorage.setItem('ageVerified', 'true')`, hide modal
- "Not yet" → `window.location.href = 'about:blank'`
- On load: check `sessionStorage.getItem('ageVerified')` — skip if `'true'`
- Keyboard navigable: focus trapped in modal, Enter/Space activate buttons

## Sound Toggle

- Web Audio API: `OscillatorNode` generating a low ambient drone (sine wave, ~80Hz, very low gain) layered with filtered noise for atmosphere
- Default: muted
- Toggle button: top-right hero, inline SVG speaker icon
- State persisted in `sessionStorage` (key: `soundEnabled`)
- Alternatively: if an audio file is provided later, swap to `<audio>` element with loop

## Form Handling

1. Client-side email validation via regex + `input.validity`
2. Invalid state: red border, inline error "Please enter a valid email address"
3. Submit: `fetch(FORM_ENDPOINT, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify({ email }) })`
4. Success: HTTP 200 or 201 → form replaced with confirmation message ("You're in." etc.)
5. Error (non-2xx or network failure): show "Something went wrong. Please try again." — email value preserved in field
6. Prevent double-submit: disable button on click, re-enable on error
7. **Dev mode fallback:** if `FORM_ENDPOINT` equals the placeholder string `'YOUR_FORM_ENDPOINT_HERE'`, log the payload to console and show the success state (allows testing without a backend)

## Analytics (GA4 + dataLayer)

All events wrapped in `if (typeof gtag !== 'undefined')` check. Parallel `dataLayer.push()` for GTM.

Events implemented per PRD Section 9.2:
- `age_gate_passed`, `age_gate_failed`
- `sound_enabled`
- `scroll_25`, `scroll_50`, `scroll_75`, `scroll_100` — fire once per page load, calculated as `scrollY / (document.body.scrollHeight - window.innerHeight)`, tracked via throttled scroll listener (100ms `requestAnimationFrame` gate, not raw scroll events)
- `prize_section_viewed`
- `cta_hero_clicked`, `cta_prize_clicked`
- `form_field_focused`
- `form_abandoned` — fires on `beforeunload` if email field has been focused but form was not submitted
- `competition_entry_submitted`, `entry_confirmation_viewed`
- `time_on_page_30s`, `time_on_page_60s`, `time_on_page_120s`
- `bottle_interaction` — fires once when user scrolls past hero section (triggering the bottle animation sequence)

## Performance Targets

- Lighthouse mobile: >= 70
- Three.js scene: < 60 draw calls
- Festival background image: lazy-loaded via `loading="lazy"` or IntersectionObserver
- Image format: WebP, < 300KB
- `will-change: transform` on animated elements only during animation

## Accessibility

- All interactive elements: `aria-label` attributes
- Form field: associated `<label>` element
- Age gate: keyboard navigable, focus trap
- CTA buttons: sufficient color contrast (white on `#E8002D` = ~4.8:1 AA pass)
- Scroll prompt and sound toggle: labeled for screen readers
- **`prefers-reduced-motion: reduce`:** When matched, all CSS transitions set to `0s` (instant), Three.js bottle rotation paused, bubble particle system disabled, scroll-driven bottle movement disabled. Check via `window.matchMedia('(prefers-reduced-motion: reduce)').matches`

## Design System Reference

All colors, typography, spacing, and component specs follow the PRD Sections 6.1–6.5 exactly. No deviations.

## Testing Strategy

- **Manual browser testing:** Chrome, Firefox, Safari, Edge at 375px and 1440px
- **Form validation:** test empty, invalid, valid email states
- **Age gate:** test both paths, session persistence, reload behavior
- **Scroll animations:** verify all IntersectionObserver triggers fire
- **Analytics:** verify events in browser console (dataLayer inspection)
- **3D rendering:** confirm Three.js canvas renders, animations smooth at 60fps
- **Accessibility:** keyboard navigation through all interactive elements

## File Structure

```
/
├── index.html          # Everything — HTML, CSS, JS
├── assets/             # (future) GLTF model, festival background image
└── docs/
    └── superpowers/
        └── specs/
            └── 2026-03-24-coca-cola-landing-page-design.md
```
