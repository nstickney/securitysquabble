# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Security Squabble — a Family Feud-style browser game about internet security best practices. Players drag-and-drop items to identify the top 5 security practices from a mixed list of 9. Pure frontend, no backend/API/database.

## Commands

```bash
pnpm dev        # Dev server
pnpm build      # Production build to dist/
pnpm lint       # ESLint
pnpm preview    # Preview production build
```

## Tech Stack

- Vue 3 with Vite 8
- vuedraggable 4 for drag-and-drop lists
- No TypeScript, no tests, no CSS framework

## Architecture

Minimal SPA with three source files:

- `src/main.js` — Vue bootstrap
- `src/App.vue` — Root component shell
- `src/components/SecuritySquabble.vue` — All game logic, state, and styling

The game component manages two draggable lists (available items and selected answers), calculates a score (0–25 points) based on correct items in correct positions, and displays educational links after scoring.

## Deployment

Production build uses `/squabble/` as the base path (configured in `vite.config.js`). The `dist/` folder is deployed as a static site to stma.is/squabble.
