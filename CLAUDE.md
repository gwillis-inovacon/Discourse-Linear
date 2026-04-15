# yoDEV Community — Discourse Theme (Linear-inspired)

## Design System

This theme uses the **Linear-inspired design system** documented in `DESIGN.md`. All SCSS changes MUST follow the design system specifications:

- **Colors**: Dark-mode-native. Forced dark via hardcoded CSS variables in `common/common.scss`. Background `#08090a`, surfaces `#0f1011` / `#191a1b`. Brand accent `#5e6ad2` / `#7170ff` only.
- **Typography**: Inter Variable with `font-feature-settings: "cv01", "ss03"`. Weights: 400 (reading), 510 (emphasis/UI), 590 (strong). Never use 700/bold.
- **Borders**: Semi-transparent white (`rgba(255,255,255,0.05)` to `rgba(255,255,255,0.08)`). Never solid dark borders.
- **Surfaces**: Translucent (`rgba(255,255,255,0.02)`). Elevation through luminance stepping, not shadows. No offset shadows, no transforms on hover.
- **No light mode**. Dark mode is forced at every level.

Read `DESIGN.md` before making any visual changes.

## Architecture

- **SCSS only** — all visual styling is in SCSS files. No JavaScript/Glimmer component changes for styling.
- **`linear-card` mixin** replaces the old `custom-card` mixin as the foundational surface treatment.
- **CSS custom properties** defined in `common/color_definitions.scss` with `--linear-*` prefix.
- **Discourse color variables** are overridden with `!important` in `common/common.scss` to force dark mode regardless of OS preference.

## Key Files

- `common/color_definitions.scss` — Linear design tokens (surfaces, text, brand, borders, shadows)
- `common/common.scss` — Font import, mixins, forced dark mode overrides, buttons, grid layout, scrollbar, typography
- `scss/header.scss` — Glass blur header
- `scss/topic-list.scss` — Borderless topic list with right thumbnails, contained pill nav
- `scss/topic.scss` — Topic view (title bar, posts wrapper, timeline)
- `scss/composer.scss` — Panel-dark composer
- `scss/right-sidebar.scss` — Right sidebar with linear-card sections
- `mobile/mobile.scss` — Mobile overrides
- `about.json` — Theme metadata and "Linear Dark" color scheme

## Important Patterns

- Topic cards use CSS Grid (`grid-template-columns: 1fr 200px`) with thumbnail in column 2
- Sidebar hover uses `--linear-accent` (`#5e6ad2`) background with white text
- Active sidebar items persist the indigo background via `.active` and `[aria-current]` selectors
- Navigation bar uses contained pill group with `rgba(94,106,210,0.15)` active state
- Header uses `backdrop-filter: blur(24px)` with translucent background

## Do Not

- Add light mode support
- Use `var(--secondary)` for text (it resolves to near-black `#08090a`)
- Use solid borders on dark backgrounds
- Use box-shadow for elevation (use background luminance stepping)
- Use font-weight above 590
