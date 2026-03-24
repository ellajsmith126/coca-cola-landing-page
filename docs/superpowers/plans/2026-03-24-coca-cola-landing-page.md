# Coca-Cola "One in a Million" Landing Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a premium, immersive competition landing page in a single `index.html` with cinematic scroll-driven animations, a hero Coke can with mouse-follow/parallax, custom cursor, ambient particles, magnetic buttons, and a single-field email entry form with GA4 analytics.

**Architecture:** Single `index.html` file — all HTML, CSS, JS inline. External CDN deps: Lenis (smooth scroll), GSAP + ScrollTrigger (animations), Google Fonts Inter. No build step. Deploys to Netlify via GitHub. Can image at `./assets/coke-can.png`.

**Tech Stack:** Vanilla HTML/CSS/JS, GSAP 3.12, ScrollTrigger, Lenis 1.1, Web Audio API, Canvas 2D, Google Fonts

**Spec:** `docs/superpowers/specs/2026-03-24-coca-cola-landing-page-design.md`

---

## File Map

| File | Responsibility |
|------|---------------|
| `index.html` | All HTML structure, inline `<style>` for all CSS, inline `<script>` for all JS |
| `assets/coke-can.png` | Hero can image (user-provided, already exists) |

Since this is a single-file project, tasks are organized by **logical feature layer** — each task adds a self-contained layer to `index.html` that can be visually verified before moving on.

---

### Task 1: HTML Head + CDN Dependencies + GA4

**Files:**
- Create: `index.html`

This task creates the HTML document shell, loads all CDN dependencies, and includes the GA4 bootstrap snippet.

- [ ] **Step 1: Create `index.html` with `<head>`, CDN links, and GA4 script**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>One in a Million | Coca-Cola</title>

  <!-- GA4 -->
  <script async src="https://www.googletagmanager.com/gtag/js?id=GA_MEASUREMENT_ID_PLACEHOLDER"></script>
  <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());
    // Measurement ID is replaced at runtime from the JS config variable.
    // We use a placeholder here; the inline script will call gtag('config', ...) with the real ID.
  </script>

  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/lenis@1.1.18/dist/lenis.min.css">
  <link rel="preload" as="image" href="./assets/coke-can.png">
```

Note: The GA4 `<script async>` tag uses a placeholder ID in the `src` attribute. In the inline JS (Task 2), after the `GA_MEASUREMENT_ID` config variable is set, call `gtag('config', GA_MEASUREMENT_ID)` to activate tracking with the real ID.

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: create index.html head with CDN deps and GA4 bootstrap"
```

---

### Task 2: Complete CSS Design System

**Files:**
- Modify: `index.html` (add `<style>` block inside `<head>`)

This task adds the full inline CSS. Every class name referenced by JS tasks is defined here. Nothing is described — every rule is provided.

- [ ] **Step 1: Add the complete `<style>` block**

```html
<style>
  /* ============================================
     CUSTOM PROPERTIES
     ============================================ */
  :root {
    --color-bg-primary: #0A0A0A;
    --color-bg-secondary: #111111;
    --color-bg-tertiary: #1A1A1A;
    --color-text-primary: #FFFFFF;
    --color-text-secondary: rgba(255,255,255,0.65);
    --color-text-tertiary: rgba(255,255,255,0.35);
    --color-accent: #E8002D;
    --color-accent-dark: #C5001F;
    --color-border: rgba(255,255,255,0.12);
    --color-success: #2ECC71;
    --color-overlay: rgba(0,0,0,0.92);

    --font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;

    --glow-intensity: 80;
  }

  /* @property for animated gradient border */
  @property --angle {
    syntax: '<angle>';
    initial-value: 0deg;
    inherits: false;
  }

  /* ============================================
     RESET & BASE
     ============================================ */
  *, *::before, *::after {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }

  html {
    scroll-behavior: auto; /* Lenis handles smooth scroll */
  }

  body {
    font-family: var(--font-family);
    font-weight: 400;
    font-size: 16px;
    line-height: 1.6;
    color: var(--color-text-primary);
    background-color: var(--color-bg-primary);
    overflow-x: hidden;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  @media (min-width: 768px) {
    body {
      font-size: 18px;
    }
  }

  /* Desktop: hide native cursor (custom cursor replaces it) */
  @media (hover: hover) {
    * {
      cursor: none !important;
    }
    .email-input {
      cursor: text !important;
    }
  }

  img {
    max-width: 100%;
    height: auto;
    display: block;
  }

  a {
    color: var(--color-text-secondary);
    text-decoration: underline;
  }

  /* ============================================
     TYPOGRAPHY
     ============================================ */
  h1 {
    font-size: 48px;
    font-weight: 800;
    letter-spacing: -0.03em;
    line-height: 1.1;
  }

  @media (min-width: 768px) {
    h1 {
      font-size: 96px;
    }
  }

  h2 {
    font-size: 30px;
    font-weight: 800;
    letter-spacing: -0.01em;
    line-height: 1.1;
  }

  @media (min-width: 768px) {
    h2 {
      font-size: 52px;
    }
  }

  .section-label {
    font-size: 12px;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.15em;
    color: var(--color-accent);
    margin-bottom: 16px;
  }

  .micro-text {
    font-size: 13px;
    color: var(--color-text-tertiary);
    margin-top: 16px;
  }

  /* ============================================
     LAYOUT UTILITIES
     ============================================ */
  .section-inner {
    max-width: 1200px;
    margin: 0 auto;
    padding: 0 24px;
  }

  .reveal-wrapper {
    overflow: hidden;
  }

  /* ============================================
     AGE GATE
     ============================================ */
  .age-gate {
    position: fixed;
    inset: 0;
    z-index: 9999;
    display: flex;
    align-items: center;
    justify-content: center;
    background: var(--color-overlay);
  }

  .age-gate-card {
    background: var(--color-bg-secondary);
    border: 1px solid var(--color-border);
    border-radius: 16px;
    padding: 48px 40px;
    max-width: 480px;
    width: 90%;
    text-align: center;
  }

  .age-gate-logo {
    font-size: 24px;
    font-weight: 800;
    color: var(--color-accent);
    margin-bottom: 24px;
    letter-spacing: -0.02em;
  }

  .age-gate-card h2 {
    font-size: 24px;
    margin-bottom: 12px;
  }

  .age-gate-card p {
    color: var(--color-text-secondary);
    font-size: 16px;
    margin-bottom: 32px;
  }

  .age-gate-buttons {
    display: flex;
    gap: 16px;
    justify-content: center;
  }

  .btn-yes,
  .btn-no {
    font-family: var(--font-family);
    font-size: 16px;
    font-weight: 700;
    padding: 14px 32px;
    border-radius: 8px;
    border: none;
    transition: transform 0.2s ease, opacity 0.2s ease;
  }

  .btn-yes {
    background: var(--color-accent);
    color: var(--color-text-primary);
  }

  .btn-yes:hover {
    background: var(--color-accent-dark);
  }

  .btn-no {
    background: transparent;
    color: var(--color-text-secondary);
    border: 1px solid var(--color-border);
  }

  .btn-no:hover {
    border-color: var(--color-text-tertiary);
  }

  /* ============================================
     HERO SECTION
     ============================================ */
  .hero {
    position: relative;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
    padding: 64px 24px;
    overflow: hidden;
  }

  @media (min-width: 768px) {
    .hero {
      padding: 100px 24px;
    }
  }

  .hero h1 {
    position: relative;
    z-index: 3;
  }

  .hero h1 span {
    display: inline-block;
  }

  .hero .subheadline {
    font-size: 18px;
    color: var(--color-text-secondary);
    margin-top: 24px;
    max-width: 480px;
    position: relative;
    z-index: 3;
  }

  @media (min-width: 768px) {
    .hero .subheadline {
      font-size: 20px;
    }
  }

  /* --- Can wrapper & image --- */
  /* .can-wrapper: GSAP ScrollTrigger animates this (y, scale, rotate, --glow-intensity) */
  /* .can-image (img inside): mouse-follow tilt applied via style.transform */
  .can-wrapper {
    position: relative;
    z-index: 2;
    margin-bottom: 32px;
    filter: drop-shadow(0 0 calc(var(--glow-intensity, 80) * 1px) rgba(232,0,45,0.4));
    will-change: transform, filter;
  }

  .can-image {
    width: 200px;
    height: auto;
    will-change: transform;
  }

  @media (min-width: 768px) {
    .can-image {
      width: 300px;
    }
  }

  /* --- CTA Buttons --- */
  .cta-btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    height: 56px;
    min-width: 240px;
    padding: 0 32px;
    background: var(--color-accent);
    color: var(--color-text-primary);
    font-family: var(--font-family);
    font-weight: 700;
    font-size: 18px;
    border: none;
    border-radius: 8px;
    text-decoration: none;
    position: relative;
    overflow: hidden;
    margin-top: 24px;
    z-index: 3;
    transition: background 0.2s ease;
  }

  .cta-btn:hover {
    background: var(--color-accent-dark);
  }

  @media (max-width: 767px) {
    .cta-btn {
      width: 100%;
    }
  }

  /* --- Sound toggle --- */
  .sound-toggle {
    position: absolute;
    top: 24px;
    right: 24px;
    z-index: 10;
    background: none;
    border: 1px solid var(--color-border);
    border-radius: 50%;
    width: 44px;
    height: 44px;
    display: flex;
    align-items: center;
    justify-content: center;
    color: var(--color-text-secondary);
    transition: border-color 0.2s ease;
  }

  .sound-toggle:hover {
    border-color: var(--color-text-primary);
  }

  .sound-toggle.active {
    border-color: var(--color-accent);
    color: var(--color-accent);
  }

  .sound-toggle svg {
    width: 20px;
    height: 20px;
    fill: currentColor;
  }

  /* --- Scroll prompt --- */
  .scroll-prompt {
    position: absolute;
    bottom: 32px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
    z-index: 3;
  }

  .scroll-prompt span {
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 0.15em;
    color: var(--color-text-tertiary);
  }

  .scroll-prompt svg {
    width: 24px;
    height: 24px;
    stroke: var(--color-text-tertiary);
    fill: none;
    stroke-width: 2;
    animation: scroll-prompt-breathing 2s ease-in-out infinite;
  }

  /* ============================================
     PRIZE REVEAL SECTION
     ============================================ */
  .prize-reveal {
    background: #0D0D0D;
    padding: 64px 24px;
    text-align: center;
  }

  @media (min-width: 768px) {
    .prize-reveal {
      padding: 100px 24px;
    }
  }

  .prize-reveal h2 {
    margin-bottom: 48px;
  }

  .prize-list {
    max-width: 700px;
    margin: 0 auto;
    display: flex;
    flex-direction: column;
    gap: 16px;
    text-align: left;
  }

  .prize-item {
    display: flex;
    align-items: center;
    gap: 16px;
    padding: 20px 24px;
    background: var(--color-bg-secondary);
    border-radius: 12px;
    border: 1px solid var(--color-border);
  }

  .prize-accent {
    width: 4px;
    height: 40px;
    background: var(--color-accent);
    border-radius: 2px;
    flex-shrink: 0;
    animation: accent-glow-pulse 3s ease-in-out infinite;
  }

  .prize-item-text {
    flex: 1;
  }

  .prize-item-text strong {
    display: block;
    font-size: 18px;
    font-weight: 700;
    margin-bottom: 4px;
  }

  .prize-item-text span {
    font-size: 14px;
    color: var(--color-text-secondary);
  }

  .transition-line {
    width: 1px;
    height: 60px;
    background: linear-gradient(to bottom, var(--color-border), transparent);
    margin: 32px auto;
  }

  .prize-reveal .cta-btn {
    margin-top: 0;
  }

  .prize-reveal .micro-text {
    margin-top: 12px;
  }

  /* ============================================
     THE FEELING SECTION
     ============================================ */
  .feeling {
    position: relative;
    overflow: hidden;
    padding: 64px 24px;
    background: linear-gradient(180deg, #1a0a00 0%, #0a0505 100%);
    text-align: center;
  }

  @media (min-width: 768px) {
    .feeling {
      padding: 100px 24px;
    }
  }

  .feeling h2 {
    position: relative;
    z-index: 3;
    text-shadow: 0 0 40px rgba(232,0,45,0.2), 0 0 80px rgba(232,0,45,0.1);
    animation: text-glow-pulse 4s ease-in-out infinite;
    margin-bottom: 32px;
  }

  .feeling p {
    position: relative;
    z-index: 3;
    max-width: 600px;
    margin: 0 auto 20px;
    color: var(--color-text-secondary);
    font-size: 18px;
    line-height: 1.7;
  }

  /* Bokeh layer */
  .bokeh-layer {
    position: absolute;
    inset: 0;
    z-index: 1;
    pointer-events: none;
  }

  .bokeh-circle {
    position: absolute;
    border-radius: 50%;
    filter: blur(30px);
  }

  /* 10 bokeh circles with individual positions/sizes/colors */
  .bokeh-circle:nth-child(1)  { width: 100px; height: 100px; top: 10%; left: 5%;  background: rgba(232,150,50,0.2); }
  .bokeh-circle:nth-child(2)  { width: 60px;  height: 60px;  top: 20%; left: 80%; background: rgba(232,180,80,0.15); }
  .bokeh-circle:nth-child(3)  { width: 120px; height: 120px; top: 50%; left: 15%; background: rgba(200,120,40,0.18); }
  .bokeh-circle:nth-child(4)  { width: 80px;  height: 80px;  top: 70%; left: 70%; background: rgba(232,160,60,0.2); }
  .bokeh-circle:nth-child(5)  { width: 40px;  height: 40px;  top: 30%; left: 50%; background: rgba(255,200,100,0.15); }
  .bokeh-circle:nth-child(6)  { width: 90px;  height: 90px;  top: 60%; left: 40%; background: rgba(232,140,50,0.22); }
  .bokeh-circle:nth-child(7)  { width: 50px;  height: 50px;  top: 15%; left: 35%; background: rgba(255,180,70,0.12); }
  .bokeh-circle:nth-child(8)  { width: 110px; height: 110px; top: 80%; left: 20%; background: rgba(200,100,30,0.2); }
  .bokeh-circle:nth-child(9)  { width: 70px;  height: 70px;  top: 40%; left: 90%; background: rgba(232,170,60,0.17); }
  .bokeh-circle:nth-child(10) { width: 30px;  height: 30px;  top: 85%; left: 60%; background: rgba(255,210,120,0.15); }

  /* Light streaks */
  .light-streak {
    position: absolute;
    z-index: 2;
    width: 2px;
    height: 200%;
    opacity: 0.06;
    pointer-events: none;
    animation: light-streak 15s linear infinite;
  }

  .light-streak:nth-child(1) {
    left: 20%;
    background: linear-gradient(180deg, transparent, rgba(232,0,45,0.3), transparent);
    transform: rotate(15deg);
    animation-delay: 0s;
  }

  .light-streak:nth-child(2) {
    left: 50%;
    background: linear-gradient(180deg, transparent, rgba(255,200,100,0.2), transparent);
    transform: rotate(-10deg);
    animation-delay: -5s;
  }

  .light-streak:nth-child(3) {
    left: 75%;
    background: linear-gradient(180deg, transparent, rgba(232,0,45,0.2), transparent);
    transform: rotate(20deg);
    animation-delay: -10s;
  }

  /* ============================================
     HOW TO ENTER SECTION
     ============================================ */
  .how-to-enter {
    background: var(--color-bg-secondary);
    padding: 64px 24px;
    text-align: center;
  }

  @media (min-width: 768px) {
    .how-to-enter {
      padding: 100px 24px;
    }
  }

  .how-to-enter h2 {
    margin-bottom: 48px;
  }

  .steps-grid {
    display: grid;
    grid-template-columns: 1fr;
    gap: 24px;
    max-width: 900px;
    margin: 0 auto;
  }

  @media (min-width: 768px) {
    .steps-grid {
      grid-template-columns: repeat(3, 1fr);
    }
  }

  .step-card {
    background: var(--color-bg-tertiary);
    border-top: 4px solid var(--color-accent);
    border-radius: 12px;
    padding: 40px 32px;
    text-align: left;
  }

  .step-number {
    font-size: 48px;
    font-weight: 800;
    color: var(--color-accent);
    line-height: 1;
    margin-bottom: 16px;
  }

  .step-card h3 {
    font-size: 20px;
    font-weight: 700;
    margin-bottom: 8px;
  }

  .step-card p {
    font-size: 15px;
    color: var(--color-text-secondary);
    line-height: 1.5;
  }

  /* ============================================
     ENTRY FORM SECTION
     ============================================ */
  .entry-form {
    padding: 64px 24px;
    text-align: center;
    background: var(--color-bg-primary);
  }

  @media (min-width: 768px) {
    .entry-form {
      padding: 100px 24px;
    }
  }

  .entry-form h2 {
    margin-bottom: 32px;
  }

  .form-container {
    max-width: 560px;
    margin: 0 auto;
  }

  /* Animated gradient border wrapper */
  .input-gradient-wrapper {
    position: relative;
    padding: 1.5px;
    border-radius: 8px;
    background: rgba(255,255,255,0.15);
    transition: background 0.3s ease;
  }

  .input-gradient-wrapper.active {
    background: conic-gradient(from var(--angle), var(--color-accent), rgba(255,255,255,0.15), var(--color-accent));
    animation: rotate-gradient 2s linear infinite;
  }

  @keyframes rotate-gradient {
    to {
      --angle: 360deg;
    }
  }

  .email-input {
    width: 100%;
    height: 56px;
    padding: 0 20px;
    background: #1E1E1E;
    border: none;
    border-radius: 7px; /* slightly less than wrapper to show gradient border */
    color: var(--color-text-primary);
    font-family: var(--font-family);
    font-size: 18px;
    outline: none;
  }

  .email-input::placeholder {
    color: var(--color-text-tertiary);
  }

  .email-input.error {
    box-shadow: 0 0 0 1.5px var(--color-accent);
  }

  .submit-btn {
    display: inline-flex;
    align-items: center;
    justify-content: center;
    height: 56px;
    min-width: 240px;
    padding: 0 32px;
    background: var(--color-accent);
    color: var(--color-text-primary);
    font-family: var(--font-family);
    font-weight: 700;
    font-size: 18px;
    border: none;
    border-radius: 8px;
    margin-top: 16px;
    position: relative;
    overflow: hidden;
    transition: background 0.2s ease;
  }

  .submit-btn:hover {
    background: var(--color-accent-dark);
  }

  .submit-btn:disabled {
    opacity: 0.6;
  }

  /* Shimmer sweep on submit button */
  .submit-btn::after {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(110deg, transparent 30%, rgba(255,255,255,0.15) 50%, transparent 70%);
    background-size: 200% 100%;
    animation: shimmer-btn 3s linear infinite;
  }

  @media (max-width: 767px) {
    .submit-btn {
      width: 100%;
    }
  }

  .form-error {
    display: none;
    color: var(--color-accent);
    font-size: 14px;
    margin-top: 8px;
    text-align: left;
  }

  .form-confirmation {
    display: none;
    padding: 32px;
  }

  .form-confirmation h3 {
    font-size: 36px;
    font-weight: 800;
    margin-bottom: 12px;
  }

  .form-confirmation p {
    color: var(--color-text-secondary);
    font-size: 18px;
  }

  .form-label {
    display: block;
    text-align: left;
    font-size: 14px;
    font-weight: 500;
    color: var(--color-text-secondary);
    margin-bottom: 8px;
  }

  /* ============================================
     FOOTER
     ============================================ */
  .footer {
    background: var(--color-bg-primary);
    padding: 48px 24px;
    text-align: center;
    border-top: 1px solid var(--color-border);
  }

  .footer-logo {
    font-size: 18px;
    font-weight: 800;
    color: var(--color-accent);
    margin-bottom: 16px;
    letter-spacing: -0.02em;
  }

  .footer p {
    font-size: 12px;
    color: var(--color-text-tertiary);
    opacity: 0.3;
    max-width: 600px;
    margin: 0 auto 12px;
    line-height: 1.5;
  }

  .footer-links {
    display: flex;
    gap: 24px;
    justify-content: center;
    margin-top: 16px;
  }

  .footer-links a {
    font-size: 12px;
    color: var(--color-text-tertiary);
  }

  /* ============================================
     CUSTOM CURSOR
     ============================================ */
  .cursor-dot {
    position: fixed;
    top: 0;
    left: 0;
    width: 8px;
    height: 8px;
    background: var(--color-accent);
    border-radius: 50%;
    pointer-events: none;
    z-index: 10000;
    transform: translate(-50%, -50%);
    opacity: 0;
    transition: opacity 0.2s ease;
  }

  .cursor-ring {
    position: fixed;
    top: 0;
    left: 0;
    width: 20px;
    height: 20px;
    border: 1px solid rgba(232,0,45,0.4);
    border-radius: 50%;
    pointer-events: none;
    z-index: 10000;
    transform: translate(-50%, -50%);
    opacity: 0;
    transition: width 0.3s ease, height 0.3s ease, border-color 0.3s ease, opacity 0.2s ease;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .cursor-ring.cursor-hover {
    width: 48px;
    height: 48px;
    background: transparent;
    border-color: var(--color-accent);
  }

  .cursor-ring.cursor-cta {
    width: 64px;
    height: 64px;
  }

  .cursor-ring.cursor-cta::after {
    content: 'ENTER';
    font-size: 10px;
    font-weight: 700;
    letter-spacing: 0.1em;
    color: var(--color-text-primary);
    text-transform: uppercase;
  }

  /* ============================================
     CANVAS STYLES
     ============================================ */
  #ambient-particles {
    position: fixed;
    inset: 0;
    z-index: 1;
    pointer-events: none;
    width: 100%;
    height: 100%;
  }

  #bubble-particles {
    position: absolute;
    inset: 0;
    z-index: 2;
    pointer-events: none;
    width: 100%;
    height: 100%;
  }

  /* ============================================
     KEYFRAME ANIMATIONS
     ============================================ */
  @keyframes shimmer-sweep {
    0% {
      background-position: -200% center;
    }
    100% {
      background-position: 200% center;
    }
  }

  /* Applied via .shimmer-active class on hero H1 */
  .shimmer-active {
    position: relative;
  }

  .shimmer-active::after {
    content: '';
    position: absolute;
    inset: 0;
    background: linear-gradient(90deg, transparent 0%, rgba(255,255,255,0.3) 50%, transparent 100%);
    background-size: 200% 100%;
    animation: shimmer-sweep 1.5s ease-in-out forwards;
    pointer-events: none;
    -webkit-background-clip: text;
    background-clip: text;
  }

  @keyframes accent-glow-pulse {
    0%, 100% {
      box-shadow: 0 0 12px rgba(232,0,45,0.3);
    }
    50% {
      box-shadow: 0 0 12px rgba(232,0,45,0.7);
    }
  }

  @keyframes light-streak {
    0% {
      transform: translateY(-50%) rotate(inherit);
    }
    100% {
      transform: translateY(0%) rotate(inherit);
    }
  }

  @keyframes scroll-prompt-breathing {
    0%, 100% {
      transform: translateY(0);
      opacity: 0.3;
    }
    50% {
      transform: translateY(8px);
      opacity: 0.6;
    }
  }

  @keyframes float {
    0%, 100% {
      transform: translateY(0);
    }
    50% {
      transform: translateY(-10px);
    }
  }

  @keyframes text-glow-pulse {
    0%, 100% {
      text-shadow: 0 0 40px rgba(232,0,45,0.2), 0 0 80px rgba(232,0,45,0.1);
    }
    50% {
      text-shadow: 0 0 50px rgba(232,0,45,0.35), 0 0 100px rgba(232,0,45,0.15);
    }
  }

  @keyframes shimmer-btn {
    0% {
      background-position: 200% center;
    }
    100% {
      background-position: -200% center;
    }
  }

  /* Mobile: can float animation (no mouse-follow on touch) */
  @media (hover: none) {
    .can-wrapper {
      animation: float 3s ease-in-out infinite;
    }
  }

  /* ============================================
     PREFERS REDUCED MOTION
     ============================================ */
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0s !important;
      animation-delay: 0s !important;
      transition-duration: 0s !important;
      transition-delay: 0s !important;
    }

    .scroll-prompt svg {
      animation: none;
    }

    .can-wrapper {
      animation: none;
      filter: drop-shadow(0 0 80px rgba(232,0,45,0.4));
    }

    #ambient-particles,
    #bubble-particles {
      display: none;
    }
  }
</style>
```

- [ ] **Step 2: Commit**

```bash
git add index.html
git commit -m "feat: add complete CSS design system with all component styles"
```

---

### Task 3: Complete HTML Markup

**Files:**
- Modify: `index.html` (add `<body>` with all section markup, plus CDN scripts at end)

This task adds the full HTML structure with all PRD copy verbatim. Every ID and class referenced by JS tasks is present.

- [ ] **Step 1: Add the complete `<body>` and all section markup**

```html
</head>
<body>

  <!-- ========== AGE GATE MODAL ========== -->
  <div class="age-gate" id="age-gate" role="dialog" aria-modal="true" aria-label="Age verification">
    <div class="age-gate-card">
      <div class="age-gate-logo">Coca-Cola</div>
      <h2>Are you 18 or over?</h2>
      <p>You must be of legal drinking age in your country to enter this competition.</p>
      <div class="age-gate-buttons">
        <button class="btn-yes" type="button" aria-label="Yes, I am 18 or over">Yes, I'm in</button>
        <button class="btn-no" type="button" aria-label="No, I am under 18">Not yet</button>
      </div>
    </div>
  </div>

  <!-- ========== AMBIENT PARTICLE CANVAS ========== -->
  <canvas id="ambient-particles" aria-hidden="true"></canvas>

  <!-- ========== HERO SECTION ========== -->
  <section class="hero" id="hero">
    <canvas id="bubble-particles" aria-hidden="true"></canvas>

    <button class="sound-toggle" aria-label="Toggle sound">
      <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
        <path d="M3 9v6h4l5 5V4L7 9H3zm13.5 3A4.5 4.5 0 0014 8.5v7a4.49 4.49 0 002.5-3.5zM14 3.23v2.06a6.51 6.51 0 010 13.42v2.06A8.5 8.5 0 0014 3.23z"/>
      </svg>
    </button>

    <div class="can-wrapper">
      <img class="can-image" src="./assets/coke-can.png" alt="Coca-Cola Classic can" />
    </div>

    <div class="reveal-wrapper">
      <h1>One in a Million.</h1>
    </div>

    <p class="subheadline">Your next Coke could win you something extraordinary. Enter for your chance to win.</p>

    <button class="cta-btn" type="button" aria-label="Enter the draw">Enter the Draw &rarr;</button>

    <p class="micro-text">No purchase necessary. T&amp;Cs apply.</p>

    <div class="scroll-prompt" aria-hidden="true">
      <span>Scroll</span>
      <svg viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
        <polyline points="6 9 12 15 18 9"></polyline>
      </svg>
    </div>
  </section>

  <!-- ========== PRIZE REVEAL SECTION ========== -->
  <section class="prize-reveal" id="prize-reveal">
    <div class="section-inner">
      <span class="section-label">What you could win</span>
      <div class="reveal-wrapper">
        <h2>Prizes That Hit Different</h2>
      </div>

      <div class="prize-list">
        <div class="prize-item" data-counter="500">
          <div class="prize-accent"></div>
          <div class="prize-item-text">
            <strong>&pound;<span class="counter-value">500</span> Cash</strong>
            <span>Deposited straight to your account</span>
          </div>
        </div>
        <div class="prize-item">
          <div class="prize-accent"></div>
          <div class="prize-item-text">
            <strong>Festival VIP Passes</strong>
            <span>Two VIP weekend passes to a major UK festival</span>
          </div>
        </div>
        <div class="prize-item">
          <div class="prize-accent"></div>
          <div class="prize-item-text">
            <strong>Exclusive Merch Drops</strong>
            <span>Limited-edition Coca-Cola streetwear collection</span>
          </div>
        </div>
        <div class="prize-item">
          <div class="prize-accent"></div>
          <div class="prize-item-text">
            <strong>Home Entertainment Bundle</strong>
            <span>Speakers, projector, and a year of streaming</span>
          </div>
        </div>
        <div class="prize-item">
          <div class="prize-accent"></div>
          <div class="prize-item-text">
            <strong>One-of-a-Kind Experiences</strong>
            <span>Money-can't-buy moments curated just for you</span>
          </div>
        </div>
      </div>

      <div class="transition-line"></div>
      <button class="cta-btn" type="button" aria-label="Enter now">Enter Now &rarr;</button>
      <p class="micro-text">Draws happen monthly. The sooner you enter, the more chances you get.</p>
    </div>
  </section>

  <!-- ========== THE FEELING SECTION ========== -->
  <section class="feeling" id="feeling">
    <div class="bokeh-layer" aria-hidden="true">
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
      <div class="bokeh-circle"></div>
    </div>

    <div class="light-streak" aria-hidden="true"></div>
    <div class="light-streak" aria-hidden="true"></div>
    <div class="light-streak" aria-hidden="true"></div>

    <div class="section-inner">
      <div class="reveal-wrapper">
        <h2>You Know the Feeling</h2>
      </div>
      <p>That first sip on a hot day. The fizz hitting your lips. The cold rushing through you. It's not just a drink — it's a moment.</p>
      <p>Now imagine that feeling, amplified. One in a million chances. One in a million prizes. One in a million you.</p>
    </div>
  </section>

  <!-- ========== HOW TO ENTER SECTION ========== -->
  <section class="how-to-enter" id="how-to-enter">
    <div class="section-inner">
      <span class="section-label">How to enter</span>
      <div class="reveal-wrapper">
        <h2>Three Simple Steps</h2>
      </div>

      <div class="steps-grid">
        <div class="step-card">
          <div class="step-number">1</div>
          <h3>Enter Your Email</h3>
          <p>Drop your email below. That's your ticket in.</p>
        </div>
        <div class="step-card">
          <div class="step-number">2</div>
          <h3>Grab a Coke</h3>
          <p>Pick up any Coca-Cola product from your local shop.</p>
        </div>
        <div class="step-card">
          <div class="step-number">3</div>
          <h3>Wait for the Draw</h3>
          <p>Winners drawn monthly. We'll email you if you've won.</p>
        </div>
      </div>
    </div>
  </section>

  <!-- ========== ENTRY FORM SECTION ========== -->
  <section class="entry-form" id="entry-form-section">
    <div class="section-inner">
      <div class="reveal-wrapper">
        <h2>Ready to Win?</h2>
      </div>

      <div class="form-container">
        <form id="entry-form" novalidate>
          <label for="email" class="form-label">Email address</label>
          <div class="input-gradient-wrapper">
            <input
              type="email"
              id="email"
              name="email"
              class="email-input"
              placeholder="you@example.com"
              required
              autocomplete="email"
              aria-label="Email address"
            />
          </div>
          <p class="form-error" role="alert"></p>
          <button type="submit" class="submit-btn">Enter the Draw &rarr;</button>
        </form>
        <p class="micro-text">By entering, you agree to the <a href="#">Official Rules</a> and <a href="#">Privacy Policy</a>. Open to residents of eligible territories, 18+.</p>

        <div class="form-confirmation" aria-live="polite">
          <h3>You're in. 🎉</h3>
          <p>Keep an eye on your inbox. If you're one in a million, we'll let you know.</p>
        </div>
      </div>
    </div>
  </section>

  <!-- ========== FOOTER ========== -->
  <footer class="footer">
    <div class="footer-logo">Coca-Cola</div>
    <p>&copy; 2026 The Coca-Cola Company. All rights reserved. Coca-Cola and the Contour Bottle are registered trademarks of The Coca-Cola Company. No purchase necessary to enter. Open to legal residents of eligible territories who are 18 years of age or older. Void where prohibited. See Official Rules for details.</p>
    <div class="footer-links">
      <a href="#">Official Rules</a>
      <a href="#">Privacy Policy</a>
      <a href="#">Terms of Service</a>
      <a href="#">Contact</a>
    </div>
  </footer>

  <!-- ========== CUSTOM CURSOR ========== -->
  <div class="cursor-dot" aria-hidden="true"></div>
  <div class="cursor-ring" aria-hidden="true"></div>

  <!-- ========== CDN SCRIPTS ========== -->
  <script src="https://cdn.jsdelivr.net/npm/lenis@1.1.18/dist/lenis.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/gsap@3.12.7/dist/gsap.min.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/gsap@3.12.7/dist/ScrollTrigger.min.js"></script>

  <!-- Inline JS begins in Task 4 -->
  <script>
  // All JS from Tasks 4–16 goes inside this single script block.
  // Each task appends to this block sequentially.
```

- [ ] **Step 2: Verify in browser — all sections render with correct styling, static page only**

Run: `python -m http.server 8080` from project root
Open: `http://localhost:8080`
Expected: All 7 sections visible with correct colors, typography, spacing, responsive layout. Age gate overlay visible. No JS functionality yet — static page only.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add complete HTML markup with all sections and PRD copy"
```

---

### Task 4: Lenis Smooth Scroll + GSAP ScrollTrigger + Config + Analytics Helper

**Files:**
- Modify: `index.html` (add to `<script>` block)

Sets up the animation engine foundation, config variables, and the `trackEvent` helper that all subsequent tasks depend on.

- [ ] **Step 1: Add configuration variables, reduced motion check, and analytics helper**

```js
// === CONFIGURATION ===
const FORM_ENDPOINT = 'YOUR_FORM_ENDPOINT_HERE';
const GA_MEASUREMENT_ID = 'YOUR_GA4_ID_HERE';
const COMPETITION_NAME = 'One in a Million';
const ELIGIBLE_TERRITORIES = '[eligible territories]';
const DRAW_FREQUENCY = 'monthly';
const CAN_IMAGE_PATH = './assets/coke-can.png';

// === GA4 CONFIG (activate with real measurement ID) ===
if (GA_MEASUREMENT_ID !== 'YOUR_GA4_ID_HERE') {
  gtag('config', GA_MEASUREMENT_ID);
}

// === REDUCED MOTION CHECK ===
const prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
const isDesktop = window.matchMedia('(hover: hover)').matches;

// === ANALYTICS HELPER ===
// Defined early so all tasks can call trackEvent() inline.
// Pushes to dataLayer (for GTM) and fires gtag (for GA4).
window.dataLayer = window.dataLayer || [];

function trackEvent(eventName, params = {}) {
  const data = { event: eventName, ...params };
  dataLayer.push(data);
  if (typeof gtag !== 'undefined') {
    gtag('event', eventName, params);
  }
}
```

- [ ] **Step 2: Initialize Lenis + GSAP ScrollTrigger sync**

```js
// === SMOOTH SCROLL ===
let lenis;
if (!prefersReducedMotion) {
  lenis = new Lenis({ lerp: 0.1, smoothWheel: true });
  lenis.on('scroll', ScrollTrigger.update);
  gsap.ticker.add((time) => { lenis.raf(time * 1000); });
  gsap.ticker.lagSmoothing(0);
} else {
  // Register ScrollTrigger without Lenis — native scroll
}
gsap.registerPlugin(ScrollTrigger);
```

- [ ] **Step 3: Verify in browser — scroll should feel buttery smooth**

Open page, scroll up and down. Scrolling should have smooth easing (no native snap). GSAP ScrollTrigger should be registered (check `console.log(ScrollTrigger)` in devtools).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add Lenis smooth scroll, GSAP ScrollTrigger, config, and analytics helper"
```

---

### Task 5: Age Gate with Cinematic Dismiss

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Add triggerHeroEntrance placeholder and implement age gate logic**

```js
// === HERO ENTRANCE PLACEHOLDER ===
// This function is defined as a no-op here. Task 7 (hero) overwrites it
// with the full character-split + entrance animation sequence.
// Age gate calls triggerHeroEntrance() on dismiss, so it must exist first.
function triggerHeroEntrance() {}

// === AGE GATE ===
const ageGate = document.getElementById('age-gate');
const ageVerified = sessionStorage.getItem('ageVerified') === 'true';

function dismissAgeGate(accepted) {
  const card = ageGate.querySelector('.age-gate-card');
  const tl = gsap.timeline({
    onComplete: () => {
      ageGate.remove();
      if (accepted) {
        sessionStorage.setItem('ageVerified', 'true');
        // ANALYTICS: age gate passed
        trackEvent('age_gate_passed');
        triggerHeroEntrance(); // defined fully in Task 7
      } else {
        // ANALYTICS: age gate failed
        trackEvent('age_gate_failed');
        window.location.href = 'about:blank';
      }
    }
  });

  if (prefersReducedMotion) {
    ageGate.remove();
    if (accepted) {
      sessionStorage.setItem('ageVerified', 'true');
      trackEvent('age_gate_passed');
      triggerHeroEntrance();
    } else {
      trackEvent('age_gate_failed');
      window.location.href = 'about:blank';
    }
    return;
  }

  // Button press feedback
  tl.to(accepted ? '.btn-yes' : '.btn-no', { scale: 1.05, duration: 0.1 });
  // Card fades and scales down
  tl.to(card, { opacity: 0, scale: 0.9, duration: 0.4, ease: 'power2.inOut' });
  // Backdrop fades
  tl.to(ageGate, { opacity: 0, duration: 0.6, ease: 'power2.inOut' }, '-=0.2');
}

if (ageVerified) {
  ageGate.remove();
  // triggerHeroEntrance() called after hero animations are defined (Task 7)
} else {
  document.querySelector('.btn-yes').addEventListener('click', () => dismissAgeGate(true));
  document.querySelector('.btn-no').addEventListener('click', () => dismissAgeGate(false));
  // Focus trap: Tab cycles between the two buttons
  const buttons = ageGate.querySelectorAll('button');
  ageGate.addEventListener('keydown', (e) => {
    if (e.key === 'Tab') {
      const focused = document.activeElement;
      if (e.shiftKey && focused === buttons[0]) { e.preventDefault(); buttons[1].focus(); }
      else if (!e.shiftKey && focused === buttons[1]) { e.preventDefault(); buttons[0].focus(); }
    }
  });
  buttons[0].focus();
}
```

- [ ] **Step 2: Verify in browser**

- Click "Yes, I'm in" -> card scales down and fades, backdrop fades, modal removed from DOM
- Reload -> age gate should NOT reappear (sessionStorage)
- Clear sessionStorage -> age gate reappears
- Click "Not yet" -> same animation, redirects to about:blank
- Keyboard: Tab cycles between buttons, Enter activates
- Check `dataLayer` in console for `age_gate_passed` / `age_gate_failed` events

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add cinematic age gate with sessionStorage persistence and analytics"
```

---

### Task 6: Custom Cursor

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Implement custom cursor**

```js
// === CUSTOM CURSOR ===
if (isDesktop && !prefersReducedMotion) {
  const cursorDot = document.querySelector('.cursor-dot');
  const cursorRing = document.querySelector('.cursor-ring');
  let mouseX = 0, mouseY = 0;
  let ringX = 0, ringY = 0;

  document.addEventListener('mousemove', (e) => {
    mouseX = e.clientX;
    mouseY = e.clientY;
    gsap.set(cursorDot, { x: mouseX, y: mouseY });
  });

  // Ring follows with lerp
  gsap.ticker.add(() => {
    ringX += (mouseX - ringX) * 0.15;
    ringY += (mouseY - ringY) * 0.15;
    gsap.set(cursorRing, { x: ringX, y: ringY });
  });

  // Hover states for interactive elements
  const interactives = document.querySelectorAll('button, a, .sound-toggle');
  interactives.forEach(el => {
    el.addEventListener('mouseenter', () => {
      cursorRing.classList.add('cursor-hover');
      if (el.classList.contains('cta-btn') || el.classList.contains('submit-btn')) {
        cursorRing.classList.add('cursor-cta');
      }
    });
    el.addEventListener('mouseleave', () => {
      cursorRing.classList.remove('cursor-hover', 'cursor-cta');
    });
  });

  // Show cursors
  document.addEventListener('mouseenter', () => {
    cursorDot.style.opacity = '1';
    cursorRing.style.opacity = '1';
  });
  document.addEventListener('mouseleave', () => {
    cursorDot.style.opacity = '0';
    cursorRing.style.opacity = '0';
  });
} else {
  // Remove cursor elements on touch devices
  document.querySelector('.cursor-dot')?.remove();
  document.querySelector('.cursor-ring')?.remove();
}
```

- [ ] **Step 2: Verify in browser**

- Red dot follows cursor immediately
- Ring follows with smooth delay
- Hover over buttons: ring expands to 48px
- Hover over CTA buttons: ring expands to 64px, "ENTER" text appears
- Native cursor should be hidden (`cursor: none`)
- Email input should show text cursor
- Resize to mobile width: no custom cursor elements

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add custom cursor with hover states and CTA text"
```

---

### Task 7: Hero Section — Can Image, Mouse-Follow, Scroll Animation, Character Split

**Files:**
- Modify: `index.html` (add to `<script>` block)

This is the biggest task — the hero is the centrepiece. Uses TWO nested elements to avoid transform conflicts: `.can-wrapper` for GSAP ScrollTrigger, `.can-image` for mouse-follow tilt.

- [ ] **Step 1: Implement character-split headline animation (overwrites placeholder)**

```js
// === CHARACTER SPLIT ===
function splitTextToChars(element) {
  const text = element.textContent;
  element.innerHTML = '';
  text.split('').forEach(char => {
    const span = document.createElement('span');
    span.textContent = char === ' ' ? '\u00A0' : char;
    span.style.display = 'inline-block';
    element.appendChild(span);
  });
  return element.querySelectorAll('span');
}

const heroH1 = document.querySelector('.hero h1');
const chars = splitTextToChars(heroH1);

// === HERO ENTRANCE ANIMATION ===
// Overwrites the placeholder from Task 5.
triggerHeroEntrance = function() {
  if (prefersReducedMotion) {
    gsap.set(chars, { opacity: 1, y: 0, rotateX: 0 });
    gsap.set('.hero .subheadline', { opacity: 1, y: 0 });
    gsap.set('.hero .cta-btn', { opacity: 1, y: 0 });
    gsap.set('.hero .micro-text', { opacity: 1 });
    gsap.set('.scroll-prompt', { opacity: 0.4 });
    return;
  }

  const tl = gsap.timeline();
  // Characters animate in
  tl.from(chars, {
    opacity: 0, y: 60, rotateX: -90,
    duration: 1.2, stagger: 0.03, ease: 'power4.out'
  });
  // Subheadline fades up
  tl.from('.hero .subheadline', {
    opacity: 0, y: 30, duration: 0.8, ease: 'power3.out'
  }, '-=0.6');
  // CTA fades up
  tl.from('.hero .cta-btn', {
    opacity: 0, y: 20, duration: 0.6, ease: 'power3.out'
  }, '-=0.4');
  // Micro text
  tl.from('.hero .micro-text', {
    opacity: 0, duration: 0.4
  }, '-=0.2');
  // Scroll prompt appears
  tl.from('.scroll-prompt', {
    opacity: 0, duration: 0.6
  }, '-=0.2');
  // Shimmer sweep after text settles
  tl.add(() => {
    heroH1.classList.add('shimmer-active');
    // Repeat shimmer every 8 seconds
    setInterval(() => {
      heroH1.classList.remove('shimmer-active');
      void heroH1.offsetWidth; // force reflow to restart animation
      heroH1.classList.add('shimmer-active');
    }, 8000);
  }, '+=0.3');
};
```

- [ ] **Step 2: Implement mouse-follow tilt on can image (inner element only)**

```js
// === MOUSE-FOLLOW CAN ===
// Mouse-follow applies to .can-image (the img) to avoid conflicting
// with GSAP ScrollTrigger transforms on .can-wrapper (the outer div).
const heroSection = document.querySelector('.hero');
const canImage = document.querySelector('.can-image');
let canTargetRotateX = 0, canTargetRotateY = 0;
let canCurrentRotateX = 0, canCurrentRotateY = 0;

if (isDesktop && !prefersReducedMotion) {
  heroSection.addEventListener('mousemove', (e) => {
    const rect = heroSection.getBoundingClientRect();
    const centerX = rect.width / 2;
    const centerY = rect.height / 2;
    const mouseXRel = e.clientX - rect.left - centerX;
    const mouseYRel = e.clientY - rect.top - centerY;
    canTargetRotateY = (mouseXRel / centerX) * 8;  // max 8deg
    canTargetRotateX = -(mouseYRel / centerY) * 8;
  });

  heroSection.addEventListener('mouseleave', () => {
    canTargetRotateX = 0;
    canTargetRotateY = 0;
  });

  gsap.ticker.add(() => {
    canCurrentRotateX += (canTargetRotateX - canCurrentRotateX) * 0.08;
    canCurrentRotateY += (canTargetRotateY - canCurrentRotateY) * 0.08;
    // Only mouse-follow tilt on .can-image; scroll transforms are on .can-wrapper
    canImage.style.transform = `perspective(1000px) rotateX(${canCurrentRotateX}deg) rotateY(${canCurrentRotateY}deg)`;
  });
}
```

- [ ] **Step 3: Implement scroll-driven can animation (targets .can-wrapper, not .can-image)**

```js
// === SCROLL-DRIVEN CAN ===
// GSAP animates .can-wrapper (outer div) so transforms don't conflict
// with the mouse-follow tilt on .can-image (inner img).
if (!prefersReducedMotion) {
  gsap.to('.can-wrapper', {
    y: -150,
    scale: 1.1,
    rotate: -5,
    '--glow-intensity': 160, // intensifies drop-shadow on scroll
    scrollTrigger: {
      trigger: '.hero',
      start: 'top top',
      end: 'bottom top',
      scrub: 1
    }
  });

  // Scroll prompt fades out
  gsap.to('.scroll-prompt', {
    opacity: 0,
    scrollTrigger: {
      trigger: '.hero',
      start: '50px top',
      end: '150px top',
      scrub: true
    }
  });
}

// Trigger hero entrance if age gate was already dismissed
if (ageVerified) {
  triggerHeroEntrance();
}
```

- [ ] **Step 4: Verify in browser**

- After age gate dismisses: characters animate in one by one with 3D rotation
- Subheadline, CTA, micro text follow in sequence
- Shimmer sweeps across headline after text settles, repeats every 8s
- Moving mouse over hero: can tilts subtly toward cursor (on .can-image)
- Scrolling: can rises, scales up, rotates (on .can-wrapper), red glow intensifies
- Scroll prompt fades out
- Mobile (narrow viewport): can has float animation, no mouse-follow
- Verify no transform conflict: mouse tilt and scroll animation work simultaneously

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add hero character-split animation, mouse-follow can, scroll animations with glow"
```

---

### Task 8: Particle Systems (Ambient Background + Hero Bubbles)

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Implement ambient particle background canvas**

```js
// === AMBIENT PARTICLES ===
if (!prefersReducedMotion) {
  const ambientCanvas = document.getElementById('ambient-particles');
  const actx = ambientCanvas.getContext('2d');
  let ambientParticles = [];

  function resizeAmbientCanvas() {
    ambientCanvas.width = window.innerWidth;
    ambientCanvas.height = window.innerHeight;
  }
  resizeAmbientCanvas();
  window.addEventListener('resize', resizeAmbientCanvas);

  // Create 100 particles
  for (let i = 0; i < 100; i++) {
    ambientParticles.push({
      x: Math.random() * window.innerWidth,
      y: Math.random() * window.innerHeight,
      radius: Math.random() * 1.5 + 0.5,
      opacity: Math.random() * 0.2 + 0.1,
      vx: (Math.random() - 0.5) * 0.3,
      vy: (Math.random() - 0.5) * 0.3
    });
  }

  function drawAmbientParticles() {
    actx.clearRect(0, 0, ambientCanvas.width, ambientCanvas.height);
    // Subtle scroll parallax
    const scrollOffset = window.scrollY * 0.05;
    ambientParticles.forEach(p => {
      p.x += p.vx;
      p.y += p.vy;
      // Wrap around edges
      if (p.x < 0) p.x = ambientCanvas.width;
      if (p.x > ambientCanvas.width) p.x = 0;
      if (p.y < 0) p.y = ambientCanvas.height;
      if (p.y > ambientCanvas.height) p.y = 0;

      actx.beginPath();
      actx.arc(p.x, p.y - scrollOffset % ambientCanvas.height, p.radius, 0, Math.PI * 2);
      actx.fillStyle = `rgba(255,255,255,${p.opacity})`;
      actx.fill();
    });
  }
}
```

- [ ] **Step 2: Implement hero bubble/fizz particle canvas**

```js
// === BUBBLE PARTICLES ===
if (!prefersReducedMotion) {
  const bubbleCanvas = document.getElementById('bubble-particles');
  const bctx = bubbleCanvas.getContext('2d');
  let bubbles = [];
  let bubblesActive = false;

  function resizeBubbleCanvas() {
    bubbleCanvas.width = bubbleCanvas.parentElement.offsetWidth;
    bubbleCanvas.height = bubbleCanvas.parentElement.offsetHeight;
  }
  resizeBubbleCanvas();
  window.addEventListener('resize', resizeBubbleCanvas);

  // Create 70 bubble particles
  function resetBubble(b) {
    const canRect = canImage.getBoundingClientRect();
    const heroRect = heroSection.getBoundingClientRect();
    b.x = canRect.left - heroRect.left + canRect.width * (0.3 + Math.random() * 0.4);
    b.y = canRect.top - heroRect.top;
    b.radius = Math.random() * 2 + 1;
    b.opacity = Math.random() * 0.5 + 0.3;
    b.speed = Math.random() * 1.5 + 0.5;
    b.drift = Math.random() * Math.PI * 2; // sine wave phase
    b.driftSpeed = Math.random() * 0.02 + 0.01;
    b.driftAmp = Math.random() * 12 + 5;
  }

  for (let i = 0; i < 70; i++) {
    const b = {};
    resetBubble(b);
    b.y = Math.random() * bubbleCanvas.height; // random start position
    bubbles.push(b);
  }

  // Activate bubbles on scroll — aligned threshold with bottle_interaction
  ScrollTrigger.create({
    trigger: '.hero',
    start: '50px top',
    onEnter: () => { bubblesActive = true; },
    onLeaveBack: () => { bubblesActive = false; }
  });

  function drawBubbles() {
    bctx.clearRect(0, 0, bubbleCanvas.width, bubbleCanvas.height);
    if (!bubblesActive) return;
    bubbles.forEach(b => {
      b.y -= b.speed;
      b.drift += b.driftSpeed;
      const driftX = Math.sin(b.drift) * b.driftAmp;
      // Fade out near top
      const fadeZone = bubbleCanvas.height * 0.2;
      const alpha = b.y < fadeZone ? (b.y / fadeZone) * b.opacity : b.opacity;
      if (b.y < -10) resetBubble(b);

      bctx.beginPath();
      bctx.arc(b.x + driftX, b.y, b.radius, 0, Math.PI * 2);
      bctx.fillStyle = `rgba(255,255,255,${Math.max(0, alpha)})`;
      bctx.fill();
    });
  }
}
```

- [ ] **Step 3: Create unified animation loop**

```js
// === UNIFIED RENDER LOOP ===
function animationLoop() {
  if (!document.hidden && !prefersReducedMotion) {
    drawAmbientParticles();
    drawBubbles();
  }
  requestAnimationFrame(animationLoop);
}
if (!prefersReducedMotion) {
  requestAnimationFrame(animationLoop);
}
```

- [ ] **Step 4: Verify in browser**

- Tiny white dots drift slowly across the entire page background
- Scroll down slightly in hero: bubble particles start rising from the can area
- Bubbles drift horizontally (sine wave), fade out near the top
- Switch to different tab and back: particles should resume
- Toggle `prefers-reduced-motion` in devtools: no particle canvases

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add ambient background particles and hero bubble fizz system"
```

---

### Task 9: Magnetic Buttons + Ripple Click Effect

**Files:**
- Modify: `index.html` (add to `<script>` block)

> **Note:** Magnetic effect applies to `.cta-btn` and `.submit-btn` only — NOT age gate buttons (`.btn-yes`, `.btn-no`), which are removed from the DOM before scroll interactions begin. Age gate buttons use standard click handling only.

- [ ] **Step 1: Implement magnetic button hover**

```js
// === MAGNETIC BUTTONS ===
// Only .cta-btn and .submit-btn get the magnetic effect.
// Age gate buttons (.btn-yes, .btn-no) are removed from DOM on dismiss,
// so they are excluded here.
if (isDesktop && !prefersReducedMotion) {
  document.querySelectorAll('.cta-btn, .submit-btn').forEach(btn => {
    btn.addEventListener('mousemove', (e) => {
      const rect = btn.getBoundingClientRect();
      const centerX = rect.left + rect.width / 2;
      const centerY = rect.top + rect.height / 2;
      const distX = e.clientX - centerX;
      const distY = e.clientY - centerY;
      const dist = Math.sqrt(distX * distX + distY * distY);
      const maxDist = 100;

      if (dist < maxDist) {
        const pull = (1 - dist / maxDist) * 12;
        const angle = Math.atan2(distY, distX);
        gsap.to(btn, {
          x: Math.cos(angle) * pull,
          y: Math.sin(angle) * pull,
          duration: 0.3, ease: 'power2.out'
        });
      }
    });

    btn.addEventListener('mouseleave', () => {
      gsap.to(btn, {
        x: 0, y: 0,
        duration: 0.4, ease: 'elastic.out(1, 0.5)'
      });
    });
  });
}
```

- [ ] **Step 2: Implement ripple click effect**

```js
// === RIPPLE CLICK ===
document.querySelectorAll('.cta-btn, .submit-btn').forEach(btn => {
  btn.style.position = 'relative';
  btn.style.overflow = 'hidden';

  btn.addEventListener('click', (e) => {
    const rect = btn.getBoundingClientRect();
    const ripple = document.createElement('span');
    const size = Math.max(rect.width, rect.height) * 2;
    ripple.style.cssText = `
      position: absolute; border-radius: 50%;
      background: rgba(255,255,255,0.3);
      width: ${size}px; height: ${size}px;
      left: ${e.clientX - rect.left - size/2}px;
      top: ${e.clientY - rect.top - size/2}px;
      transform: scale(0); pointer-events: none;
    `;
    btn.appendChild(ripple);
    gsap.to(ripple, {
      scale: 4, opacity: 0, duration: 0.6,
      ease: 'power2.out',
      onComplete: () => ripple.remove()
    });
  });
});
```

- [ ] **Step 3: Verify in browser**

- Move cursor near a CTA button: button pulls toward cursor
- Move cursor away: button snaps back with elastic bounce
- Click: ripple expands from click point and fades out
- Mobile: no magnetic effect (buttons work normally)

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add magnetic button hover and ripple click effects"
```

---

### Task 10: Prize Reveal Section Animations (Stagger, Glow, Counter)

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Implement prize item stagger animation + accent bar pulse**

```js
// === PRIZE REVEAL ANIMATIONS ===
if (!prefersReducedMotion) {
  // Section label letter-spacing animation
  gsap.from('.prize-reveal .section-label', {
    opacity: 0, letterSpacing: '0.3em',
    duration: 0.8, ease: 'power3.out',
    scrollTrigger: { trigger: '.prize-reveal', start: 'top 80%' }
  });

  // H2 clip-reveal
  gsap.from('.prize-reveal h2', {
    y: '100%', duration: 0.8, ease: 'power3.out',
    scrollTrigger: { trigger: '.prize-reveal h2', start: 'top 85%' }
  });

  // Prize items stagger in
  gsap.from('.prize-item', {
    opacity: 0, y: 40, scale: 0.95,
    duration: 0.7, stagger: 0.15, ease: 'power3.out',
    scrollTrigger: { trigger: '.prize-list', start: 'top 80%' },
    onComplete: function() {
      // Flash accent bars
      gsap.fromTo('.prize-accent',
        { opacity: 0.5 }, { opacity: 1, duration: 0.3, stagger: 0.1 }
      );
    }
  });

  // Transition line + CTA
  gsap.from('.prize-reveal .transition-line', {
    opacity: 0, y: 20, duration: 0.6,
    scrollTrigger: { trigger: '.transition-line', start: 'top 85%' }
  });
}
```

- [ ] **Step 2: Implement GBP500 counter animation**

```js
// === £500 COUNTER ===
if (!prefersReducedMotion) {
  const spendingItem = document.querySelector('[data-counter]');
  if (spendingItem) {
    const counterObj = { val: 0 };
    ScrollTrigger.create({
      trigger: spendingItem,
      start: 'top 80%',
      once: true,
      onEnter: () => {
        gsap.to(counterObj, {
          val: 500, duration: 1.5, ease: 'power2.out',
          onUpdate: () => {
            spendingItem.querySelector('.counter-value').textContent =
              Math.round(counterObj.val);
          }
        });
      }
    });
  }
}
```

Note: The HTML for the GBP500 prize item has a `data-counter="500"` attribute and a `.counter-value` span wrapping the number. The `£` symbol is outside the span so the counter only animates the number.

- [ ] **Step 3: Verify in browser**

- Scroll to prize section: label animates with letter-spacing settling
- H2 reveals upward from behind mask
- Prize items stagger in with scale + fade, accent bars flash on arrival
- Accent bars pulse with red glow continuously
- "£500" counts up from £0 over 1.5s
- Scroll up and down: animations only play once (not re-trigger)

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "feat: add prize section stagger animations and £500 counter"
```

---

### Task 11: The Feeling Section — Multi-Layer Parallax + Glowing Text

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Implement parallax layers and text animations**

```js
// === FEELING SECTION PARALLAX ===
if (!prefersReducedMotion) {
  // Bokeh circles parallax (slower scroll rate)
  gsap.to('.feeling .bokeh-layer', {
    y: -80,
    scrollTrigger: {
      trigger: '.feeling',
      start: 'top bottom',
      end: 'bottom top',
      scrub: true
    }
  });

  // H2 clip-reveal
  gsap.from('.feeling h2', {
    y: '100%', duration: 0.8, ease: 'power3.out',
    scrollTrigger: { trigger: '.feeling h2', start: 'top 85%' }
  });

  // Body paragraphs fade in
  gsap.from('.feeling p', {
    opacity: 0, y: 30, duration: 0.7, stagger: 0.2, ease: 'power3.out',
    scrollTrigger: { trigger: '.feeling p', start: 'top 85%' }
  });
}
```

- [ ] **Step 2: Verify in browser**

- Scroll to feeling section: bokeh circles move at slower rate than text (parallax depth)
- Light streaks animate diagonally (CSS animation, should already work from Task 2 CSS)
- H2 text has red glow that pulses subtly (CSS animation from Task 2)
- H2 reveals upward, paragraphs fade in with stagger

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add feeling section parallax and text reveal animations"
```

---

### Task 12: How to Enter Section Animations

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Implement step card slide-up animations**

```js
// === HOW TO ENTER ===
if (!prefersReducedMotion) {
  gsap.from('.how-to-enter .section-label', {
    opacity: 0, letterSpacing: '0.3em',
    duration: 0.8, ease: 'power3.out',
    scrollTrigger: { trigger: '.how-to-enter', start: 'top 80%' }
  });

  gsap.from('.how-to-enter h2', {
    y: '100%', duration: 0.8, ease: 'power3.out',
    scrollTrigger: { trigger: '.how-to-enter h2', start: 'top 85%' }
  });

  gsap.from('.step-card', {
    opacity: 0, y: 60, duration: 0.7, stagger: 0.15, ease: 'power3.out',
    scrollTrigger: { trigger: '.steps-grid', start: 'top 80%' }
  });
}
```

- [ ] **Step 2: Verify — cards slide up on scroll with stagger**

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add how-to-enter section scroll animations"
```

---

### Task 13: Entry Form — Spotlight, Gradient Border, Shimmer, Validation

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Implement spotlight activation on scroll**

```js
// === ENTRY FORM SPOTLIGHT ===
if (!prefersReducedMotion) {
  const formSection = document.querySelector('.entry-form');
  gsap.fromTo(formSection,
    { backgroundImage: 'radial-gradient(ellipse at center, rgba(26,0,0,0) 0%, #0A0A0A 70%)' },
    {
      backgroundImage: 'radial-gradient(ellipse at center, #1A0000 0%, #0A0A0A 70%)',
      duration: 1.2, ease: 'power2.inOut',
      scrollTrigger: { trigger: formSection, start: 'top 70%', once: true }
    }
  );

  // Form elements fade in
  gsap.from('.entry-form h2', {
    y: '100%', duration: 0.8, ease: 'power3.out',
    scrollTrigger: { trigger: '.entry-form h2', start: 'top 85%' }
  });

  gsap.from('.form-container', {
    opacity: 0, y: 30, duration: 0.8, ease: 'power3.out',
    scrollTrigger: { trigger: '.form-container', start: 'top 85%' }
  });
}
```

- [ ] **Step 2: Implement animated gradient border on focus**

```js
// === GRADIENT BORDER ===
const inputWrapper = document.querySelector('.input-gradient-wrapper');
const emailInput = document.getElementById('email');

emailInput.addEventListener('focus', () => {
  inputWrapper.classList.add('active');
  // ANALYTICS: form field focused (fire once)
  if (!formFocused) { trackEvent('form_field_focused'); formFocused = true; }
});
emailInput.addEventListener('blur', () => {
  inputWrapper.classList.remove('active');
});
```

Note: The `.input-gradient-wrapper` uses `@property --angle` and `conic-gradient` defined in Task 2 CSS. The `.active` class triggers the CSS animation.

- [ ] **Step 3: Implement form validation and submission**

```js
// === FORM HANDLING ===
const form = document.getElementById('entry-form');
const submitBtn = document.querySelector('.submit-btn');
const errorMsg = document.querySelector('.form-error');
const confirmationMsg = document.querySelector('.form-confirmation');
const formContainer = document.querySelector('.form-container');
let formFocused = false, formSubmitted = false;

function isValidEmail(email) {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}

form.addEventListener('submit', async (e) => {
  e.preventDefault();
  const email = emailInput.value.trim();

  // Validate
  if (!isValidEmail(email)) {
    emailInput.classList.add('error');
    errorMsg.textContent = 'Please enter a valid email address';
    errorMsg.style.display = 'block';
    return;
  }

  emailInput.classList.remove('error');
  errorMsg.style.display = 'none';
  submitBtn.disabled = true;
  submitBtn.textContent = 'Entering...';

  // Dev mode fallback
  if (FORM_ENDPOINT === 'YOUR_FORM_ENDPOINT_HERE') {
    console.log('DEV MODE — Form payload:', { email });
    showConfirmation();
    return;
  }

  try {
    const res = await fetch(FORM_ENDPOINT, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email })
    });
    if (res.ok) {
      showConfirmation();
    } else {
      showFormError('Something went wrong. Please try again.');
    }
  } catch {
    showFormError('Something went wrong. Please try again.');
  }
});

function showConfirmation() {
  formContainer.querySelector('form').style.display = 'none';
  confirmationMsg.style.display = 'block';
  // ANALYTICS: form submitted + confirmation viewed
  trackEvent('competition_entry_submitted');
  formSubmitted = true;
  trackEvent('entry_confirmation_viewed');
  if (!prefersReducedMotion) {
    gsap.from(confirmationMsg, { opacity: 0, scale: 0.9, duration: 0.5, ease: 'back.out(1.5)' });
  }
}

function showFormError(msg) {
  submitBtn.disabled = false;
  submitBtn.textContent = 'Enter the Draw \u2192';
  errorMsg.textContent = msg;
  errorMsg.style.display = 'block';
}

// Clear error on input
emailInput.addEventListener('input', () => {
  emailInput.classList.remove('error');
  errorMsg.style.display = 'none';
});
```

- [ ] **Step 4: Verify in browser**

- Scroll to form section: red spotlight glow fades in, form elements slide up
- Click into email field: animated gradient border sweeps around the input
- Click out: border returns to static
- Submit empty: "Please enter a valid email address" error, red border
- Submit invalid: same error
- Submit valid email: confirmation "You're in." appears with scale animation
- Check console for DEV MODE log
- Submit button shows "Entering..." and disables during submission
- Check `dataLayer` for `form_field_focused`, `competition_entry_submitted`, `entry_confirmation_viewed`

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: add entry form spotlight, gradient border, validation, submission, and analytics"
```

---

### Task 14: Sound Toggle

**Files:**
- Modify: `index.html` (add to `<script>` block)

- [ ] **Step 1: Implement Web Audio ambient sound**

```js
// === SOUND TOGGLE ===
const soundToggle = document.querySelector('.sound-toggle');
let audioCtx, isPlaying = false;

function createAmbientAudio() {
  audioCtx = new (window.AudioContext || window.webkitAudioContext)();

  // Low drone
  const osc = audioCtx.createOscillator();
  osc.type = 'sine';
  osc.frequency.value = 80;

  const gain = audioCtx.createGain();
  gain.gain.value = 0.03; // Very quiet

  // Filtered noise for texture
  const bufferSize = 2 * audioCtx.sampleRate;
  const noiseBuffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
  const output = noiseBuffer.getChannelData(0);
  for (let i = 0; i < bufferSize; i++) {
    output[i] = Math.random() * 2 - 1;
  }
  const noise = audioCtx.createBufferSource();
  noise.buffer = noiseBuffer;
  noise.loop = true;

  const noiseFilter = audioCtx.createBiquadFilter();
  noiseFilter.type = 'lowpass';
  noiseFilter.frequency.value = 200;

  const noiseGain = audioCtx.createGain();
  noiseGain.gain.value = 0.01;

  osc.connect(gain).connect(audioCtx.destination);
  noise.connect(noiseFilter).connect(noiseGain).connect(audioCtx.destination);
  osc.start();
  noise.start();

  return { osc, noise, gain, noiseGain };
}

let audioNodes;
soundToggle.addEventListener('click', () => {
  if (!audioCtx) audioNodes = createAmbientAudio();

  if (isPlaying) {
    audioCtx.suspend();
    isPlaying = false;
    soundToggle.classList.remove('active');
    sessionStorage.setItem('soundEnabled', 'false');
  } else {
    audioCtx.resume();
    isPlaying = true;
    soundToggle.classList.add('active');
    sessionStorage.setItem('soundEnabled', 'true');
    // ANALYTICS: sound enabled
    trackEvent('sound_enabled');
  }
});

// Restore sound state
if (sessionStorage.getItem('soundEnabled') === 'true') {
  // Don't autoplay — browsers block it. Show visual hint that sound was on.
  soundToggle.classList.add('was-active');
}
```

- [ ] **Step 2: Verify — click sound toggle, hear low ambient drone; click again to mute**

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add Web Audio ambient sound toggle with analytics"
```

---

### Task 15: Analytics — Scroll Depth, Time-on-Page, and Remaining Events

**Files:**
- Modify: `index.html` (add to `<script>` block)

Most analytics calls are already inline in their respective tasks (age gate, sound, form focus, form submit). This task adds the remaining page-level events: scroll depth thresholds, time-on-page, prize section viewed, CTA clicks, bottle interaction, and form abandonment.

- [ ] **Step 1: Implement scroll depth, time-on-page, and remaining events**

```js
// === ANALYTICS — SCROLL DEPTH ===
const scrollThresholds = { 25: false, 50: false, 75: false, 100: false };
let scrollTicking = false;
window.addEventListener('scroll', () => {
  if (scrollTicking) return;
  scrollTicking = true;
  requestAnimationFrame(() => {
    const scrollHeight = document.body.scrollHeight - window.innerHeight;
    if (scrollHeight > 0) {
      const pct = (window.scrollY / scrollHeight) * 100;
      [25, 50, 75, 100].forEach(threshold => {
        if (pct >= threshold && !scrollThresholds[threshold]) {
          scrollThresholds[threshold] = true;
          trackEvent(`scroll_${threshold}`);
        }
      });
    }
    scrollTicking = false;
  });
});

// === ANALYTICS — PRIZE SECTION VIEWED ===
ScrollTrigger.create({
  trigger: '.prize-reveal',
  start: 'top 80%',
  once: true,
  onEnter: () => trackEvent('prize_section_viewed')
});

// === ANALYTICS — CTA CLICKS ===
document.querySelectorAll('.hero .cta-btn').forEach(btn =>
  btn.addEventListener('click', () => trackEvent('cta_hero_clicked'))
);
document.querySelectorAll('.prize-reveal .cta-btn').forEach(btn =>
  btn.addEventListener('click', () => trackEvent('cta_prize_clicked'))
);

// === ANALYTICS — FORM ABANDONED ===
window.addEventListener('beforeunload', () => {
  if (formFocused && !formSubmitted) trackEvent('form_abandoned');
});

// === ANALYTICS — BOTTLE INTERACTION ===
// Aligned with bubble activation threshold (50px)
ScrollTrigger.create({
  trigger: '.hero',
  start: '50px top',
  once: true,
  onEnter: () => trackEvent('bottle_interaction')
});

// === ANALYTICS — TIME ON PAGE ===
setTimeout(() => trackEvent('time_on_page_30s'), 30000);
setTimeout(() => trackEvent('time_on_page_60s'), 60000);
setTimeout(() => trackEvent('time_on_page_120s'), 120000);
```

- [ ] **Step 2: Verify in browser console**

Open devtools -> Console. Interact with the page:
- Dismiss age gate -> `dataLayer` should contain `age_gate_passed` (from Task 5)
- Scroll past 50px -> `bottle_interaction`
- Scroll to 25% -> `scroll_25`
- Click hero CTA -> `cta_hero_clicked`
- Focus email field -> `form_field_focused` (from Task 13)
- Submit form -> `competition_entry_submitted`, `entry_confirmation_viewed` (from Task 13)
- Wait 30s -> `time_on_page_30s`

Check: `console.log(dataLayer)` should show all fired events.

- [ ] **Step 3: Commit**

```bash
git add index.html
git commit -m "feat: add scroll depth, time-on-page, and remaining analytics events"
```

---

### Task 16: CTA Scroll-to-Form + Section H2 Overflow Wrappers

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Wire all CTA buttons to scroll to the entry form**

```js
// === CTA SCROLL TO FORM ===
document.querySelectorAll('.cta-btn').forEach(btn => {
  btn.addEventListener('click', (e) => {
    e.preventDefault();
    const formSection = document.querySelector('.entry-form');
    if (lenis) {
      lenis.scrollTo(formSection, { offset: -50 });
    } else {
      formSection.scrollIntoView({ behavior: 'smooth' });
    }
  });
});
```

- [ ] **Step 2: Ensure all H2 elements have overflow:hidden wrappers in HTML for clip-reveal**

Check that each H2 that uses the clip-reveal animation is wrapped in a `<div class="reveal-wrapper">` (which has `overflow: hidden` in CSS from Task 2). This is already present in the HTML from Task 3 — verify and fix if needed.

- [ ] **Step 3: Close the script tag and body**

```js
  // End of inline script
  </script>
</body>
</html>
```

- [ ] **Step 4: Verify — clicking any CTA smooth-scrolls to the entry form**

- [ ] **Step 5: Commit**

```bash
git add index.html
git commit -m "feat: wire CTAs to scroll-to-form and verify H2 clip-reveal wrappers"
```

---

### Task 17: Final Polish — Reduced Motion, Performance, Accessibility Audit

**Files:**
- Modify: `index.html`

- [ ] **Step 1: Test and fix `prefers-reduced-motion` path**

In devtools: Rendering -> Emulate CSS media -> prefers-reduced-motion: reduce

Verify:
- Lenis disabled, native scroll active
- No character-split animation (instant text)
- No particle canvases
- No mouse-follow or magnetic effects
- No custom cursor
- Can is static (no scroll animation, no tilt)
- All GSAP animations instant
- Form works normally
- Age gate dismiss is instant

Fix any animations that still play.

- [ ] **Step 2: Accessibility pass**

- Tab through entire page: every interactive element reachable
- Age gate: focus trap works, Enter activates buttons
- All buttons have `aria-label` attributes
- Form input has associated `<label>`
- Sound toggle has `aria-label="Toggle sound"`
- Scroll prompt has `aria-hidden="true"` (decorative)
- Images have `alt` attributes (can image: `alt="Coca-Cola Classic can"`)

- [ ] **Step 3: Performance check**

- Open Lighthouse in devtools -> run mobile audit
- Target: >= 65 performance score
- Check: no layout shift from animations (CLS)
- Check: can image preloaded (no LCP delay)
- Check: no jank during scroll (60fps in Performance tab)
- If particle systems cause jank: reduce particle count

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "fix: accessibility, reduced motion, and performance polish"
```

---

### Task 18: Browser Testing + Final Commit

**Files:**
- Modify: `index.html` (bug fixes only)

- [ ] **Step 1: Test at 375px (mobile)**

- Age gate: buttons stack vertically
- Hero: can is 200px wide, float animation (no mouse-follow)
- Text sizes are mobile-specific (H1 48px, H2 30px, etc.)
- CTA buttons are full-width
- Step cards stack vertically
- Form is full-width
- No custom cursor
- No horizontal overflow anywhere

- [ ] **Step 2: Test at 1440px (desktop)**

- All sections at correct max-widths
- Can is ~300px wide with mouse-follow tilt on .can-image
- Scroll animation on .can-wrapper (y, scale, rotate, glow intensification)
- Both transforms work simultaneously without conflict
- 3-column step cards
- Custom cursor active
- All scroll animations fire correctly
- Shimmer repeats on H1 every 8 seconds

- [ ] **Step 3: Fix any issues found**

- [ ] **Step 4: Final commit**

```bash
git add index.html
git commit -m "chore: browser testing fixes and final polish"
```
