# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

JDK Studios is a personal portfolio site for DJ JDK — a live sound engineer, studio engineer, photographer, and visual designer based in Connecticut. It is a **pure static site** with no build tooling, no package manager, and no framework. All code is plain HTML5, CSS (inline in each HTML file), and vanilla JavaScript.

## Development

Since there is no build step, development is just opening the HTML files in a browser. There are no install steps, no compile steps, and no test suite. To preview changes:

```bash
# Any local HTTP server works — Python's is convenient
python3 -m http.server 8080
# Then open http://localhost:8080
```

Deployment is automatic: pushing to `main` triggers `.github/workflows/static.yml`, which publishes the entire repo to GitHub Pages.

## Architecture

### Two distinct UIs in two files

**`index.html`** (1,333 lines) — the primary portfolio, dark modern aesthetic.
- Sections in order: splash screen → fixed nav → hero → about → work (tabs) → contact → footer.
- The "Work" section has 5 tabs: *Live Sound*, *Studio*, *Photography*, *Thumbnails*, *Edits*. Tab content is always in the DOM; photos use deferred lazy-loading (populated on first tab open via `data-src` → `src`).
- YouTube embeds in the *Edits* tab fetch thumbnails via `maxresdefault.jpg` with a fallback to `hqdefault.jpg`.
- The contact form posts to Formspree (`f/mojprjgd`).

**`desktop-view.html`** (2,375 lines) — a hidden retro Windows 98 desktop easter egg, self-contained.
- Fully functional simulated desktop: draggable/resizable windows, a Start menu, taskbar, and several mini-apps (calculator, minesweeper, 2048, image viewer, file explorer, settings).
- Mirrors the portfolio content (Live Sound, Studio, Photography) inside a faux file system.
- Not linked from `index.html` by default — accessed by navigating directly to `/desktop-view.html`.

All images live in `images/` and are referenced with relative paths.

## Design System (index.html)

All colors are CSS custom properties declared on `:root`:

| Variable | Value | Usage |
|---|---|---|
| `--black` | `#060810` | Page background |
| `--surface` | `#0a0d16` | Section backgrounds |
| `--card` | `#0e1220` | Cards, form inputs |
| `--blue` | `#5eb8e8` | Primary accent / highlights |
| `--blue-dim` | `#3a7ca8` | Secondary accent |
| `--blue-glow` | `rgba(94,184,232,0.12)` | Subtle glow effects |
| `--ash` | `#8a9ab0` | Muted text |
| `--white` | `#e8eef5` | Primary text |

Fonts: **Bebas Neue** (headings), **DM Mono** (labels/nav), **Cormorant Garamond** (body) — all from Google Fonts.

Single responsive breakpoint: `max-width: 900px` switches to mobile layout (single-column, hamburger menu).

## Key JavaScript Patterns

- **Splash screen**: auto-dismisses after 2.4 s; suppressed on return visits via `sessionStorage`. Can be skipped with Enter.
- **Scroll reveals**: `IntersectionObserver` adds `.revealed` class to trigger CSS animations. Staggered `transition-delay` is applied inline for grid children.
- **Custom cursor**: a dot + ring pair that follows `mousemove`; hidden on touch devices via `@media (hover: none)`.
- **Lazy-loaded photo grid**: images have `data-src` instead of `src`; a separate `IntersectionObserver` swaps them on scroll. Batch `decode()` is used for smooth loading.
- **Lightbox**: clicking a photo card opens a full-screen overlay; Escape or clicking outside closes it.

`desktop-view.html` manages all window state (position, z-index, active/minimized) in plain JS arrays with no external libraries.
