# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A static, dependency-free news module for MEF University's CELT (Center for English Language Teaching). All logic — HTML, CSS, and JS — is inline in single HTML files. There is no build system, no package manager, and no external dependencies beyond Google Fonts and third-party CDN embeds.

## Development

**No build step.** Open any HTML file directly in a browser. `index.html` is the canonical production version.

**Testing changes:** Open `index.html` in a browser. To test Turkish language mode, append `?lang=tr` to the URL.

## File Roles

| File | Purpose |
|---|---|
| `index.html` | Canonical production file — primary target for changes |
| `CELT News Module.html` | Earlier variant, kept for reference |
| `CELT News Module (standalone).html` | Enhanced standalone version with CSS custom properties for background |
| `CELT News Module (offline).html` | Bundled version with base64-embedded assets (612KB, not for editing) |
| `CELT News Module Wireframes.html` | Design reference only, not functional |

## Architecture

**Single-file pattern:** Each functional variant is self-contained — styles in `<style>`, scripts in `<script>`, no external `.css` or `.js` files. Changes to one file do not propagate to others automatically.

**CSS custom properties** drive the theme. Key variables (defined in `:root`):
- `--blue: #3B4AC8` — CELT primary
- `--coral: #E84855` — accent
- `--linkedin: #0A66C2`
- `--celt-bg-url` — overridable background image URL (used in standalone version)

**Layout:** Flexbox throughout; CSS Grid for the bottom row (`1fr 2fr` split: Bulletin card left, LinkedIn feed right). Max-width 960px container. Single responsive breakpoint at 600px.

**Fonts:** DM Sans, DM Serif Display, Inter — all loaded from Google Fonts CDN.

## Language Switching (EN/TR)

Triggered by the `?lang=tr` URL parameter. The translation dictionary is the `TR` object near the bottom of each file's `<script>` block. Translation works by walking the entire DOM tree and replacing text nodes that match dictionary keys.

A retry loop (`tryApply(retries)`) re-runs the walk 6 times at 500ms intervals to catch content rendered late by Curator.io.

When adding new visible text strings, add a corresponding entry to `TR` in every file that displays that text.

## Edit Mode / Tweaks Panel

A hidden `#tweaks-panel` div exposes accent-color presets and a description textarea. It is activated by a `postMessage` from the parent frame (`{type: '__activate_edit_mode'}`), making it usable in iframe-embedded contexts. Saved state is posted back to the parent via `__edit_mode_set_keys`.

`TWEAK_DEFAULTS` holds the initial accent color and description text — change these to update defaults.

## External Integrations

- **Curator.io LinkedIn feed** — loaded via async script from CDN; feed ID: `579820dc-fc14-4684-b98a-b6eadae1a234`
- **Google Drive bulletin thumbnail** — dynamically constructed URL from Drive file ID `1yLQNpvFkR2AmDx5gfrUqQBlOkb6Po2k7`; falls back to a CSS-drawn placeholder on `onerror`
- **Social links** — MEF CELT LinkedIn, Instagram, X, YouTube (hardcoded `href` attributes)
- **Workshop/Bulletins links** — Google Sites and MEF University URLs (hardcoded)

## Key Conventions

- All state changes happen via `element.style.setProperty()` or direct DOM manipulation — no framework, no virtual DOM.
- `onclick` handlers are written inline in HTML, not via `addEventListener`.
- Global scope is used freely; no module pattern except the Curator.io IIFE loader.
- Optional chaining (`?.`) is used — requires a modern browser; do not add polyfills.
