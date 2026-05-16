# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is a single-file HTML/CSS/JS dream journal web app (`index.html`) with no build system, no dependencies, and no package manager. Open the file directly in a browser to run it.

## Architecture

The entire application lives in `index.html` — HTML structure, CSS (in `<style>`), and JavaScript (in `<script>`) are all inline.

### Storage

The app uses `window.storage.get(key)` / `window.storage.set(key, value)` — a non-standard async API provided by the hosting platform, **not** standard `localStorage`. All journal entries are stored under the key `dreamjournal_she_v1` as a JSON-serialised array.

### Anthropic API calls

The app calls `https://api.anthropic.com/v1/messages` directly from the browser with no `x-api-key` header — the hosting platform injects authentication. The model in use is `claude-sonnet-4-20250514`. Three features make API calls:

- **Transcribe with AI** — sends audio blobs as base64 `document` content blocks; supports English, Croatian, and Albanian via language-specific system prompts
- **Interpret this dream** — sends the transcript text for Jungian-style dream analysis
- **Start AI Analysis** (Patterns tab) — sends all stored transcripts (up to 8000 chars) for cross-entry pattern analysis

### UI structure

Three tabs driven by `switchTab(name)`:
1. **Record Today** — `MediaRecorder` voice capture, transcript textarea, AI interpretation, save button
2. **Journal** — searchable list of saved entries rendered by `renderDiary()`; cards expand/collapse via `toggleCard(id)`
3. **Patterns & Analysis** — single button that fires the cross-entry AI call

A daily poem is selected deterministically from a 31-poem array using `dayOfYear % POEMS.length`. Moon phase and sun position are computed from the current date/time using simple astronomical formulas and displayed as "cosmic badges".

### Poem template syntax

Poem `lines` strings use `<n>Name</n>` as a custom tag that `renderPoem()` converts to `<span class="name-highlight">Name</span>`. The name used throughout all poems is `Artan`.

## CSS conventions

- CSS custom properties defined in `:root` (e.g. `--ink`, `--paper`, `--rose`, `--accent`, `--moon`) are used throughout — update colours only there
- Font stack: `'Cormorant Garamond'` (serif body) and `'JetBrains Mono'` (monospace labels/UI) loaded from Google Fonts CDN
