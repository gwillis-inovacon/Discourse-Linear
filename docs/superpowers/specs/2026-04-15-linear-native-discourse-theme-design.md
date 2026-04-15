# Linear-Native Discourse Theme — Design Spec

## Overview

A complete SCSS rewrite of the Discourse-Linear theme to achieve a native Linear aesthetic. The structural JavaScript/Glimmer components (sidebar, topic list items, banners, post bar) remain unchanged. Only the visual SCSS layer is rewritten.

The theme ships with a self-contained "Linear Dark" color scheme in `about.json`.

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Topic cards | Borderless list, thumbnail right | Content-first, high density, true Linear feel |
| Header | Glass blur | Consistency with yoDEV Homepage, modern feel |
| Right sidebar | Keep, restyle | Adds discovery value, just needs Linear surfaces |
| Interactive elements | Subtle depth | Contained pill nav, soft CTA glow, panel-dark composer. Tactile but clean |

## Design System

### Typography

- **Primary font**: `Inter Variable` with fallbacks: `Inter, SF Pro Display, -apple-system, system-ui, sans-serif`
- **Monospace**: `Berkeley Mono, ui-monospace, SF Mono, Menlo, monospace`
- **OpenType features**: `"cv01", "ss03"` enabled globally
- **Weight system**: 400 (reading), 510 (emphasis/UI default), 590 (strong emphasis)
- **Letter spacing**: Negative at display sizes (-0.02em on headings), normal on body
- **No weight above 590** — never use 700/bold

### Color Palette

#### Surfaces (luminance stepping)
| Token | Value | Use |
|-------|-------|-----|
| `--linear-bg` | `#08090a` | Page background, deepest canvas |
| `--linear-panel` | `#0f1011` | Header, sidebar backgrounds, alternating rows |
| `--linear-surface` | `#191a1b` | Elevated surfaces, composer, cards |
| `--linear-surface-secondary` | `#28282c` | Hover states, toggle backgrounds |

#### Text
| Token | Value | Use |
|-------|-------|-----|
| `--linear-text-primary` | `#f7f8f8` | Headings, primary content |
| `--linear-text-secondary` | `#d0d6e0` | Body text, descriptions |
| `--linear-text-tertiary` | `#8a8f98` | Metadata, timestamps, placeholders |
| `--linear-text-quaternary` | `#62666d` | Disabled, de-emphasized |

#### Brand
| Token | Value | Use |
|-------|-------|-----|
| `--linear-accent` | `#5e6ad2` | Primary CTA backgrounds, brand elements |
| `--linear-accent-bright` | `#7170ff` | Links, active states, hover accents |
| `--linear-accent-hover` | `#828fff` | Hover on accent elements |

#### Borders
- **Default**: `1px solid rgba(255,255,255,0.05)` — whisper-thin, barely visible
- **Standard**: `1px solid rgba(255,255,255,0.08)` — cards, inputs
- **Emphasis**: `1px solid rgba(255,255,255,0.12)` — hover states
- **Active/Focus**: `1px solid var(--linear-accent-bright)` — interactive focus

### Border Radius Scale
| Context | Value |
|---------|-------|
| Buttons, inputs | 6px |
| Cards, containers | 8px |
| Panels, composer | 10-12px |
| Pills, tags | 9999px |
| Avatars | 50% |

### Shadows
No offset shadows. Elevation through luminance stepping.
- **Subtle**: `0 1px 3px rgba(0,0,0,0.2)` — soft glow on primary buttons
- **Panel**: `0 4px 12px rgba(0,0,0,0.2)` — composer, dropdowns
- **Ring**: `0 0 0 1px rgba(0,0,0,0.2)` — card boundary (border-as-shadow)

## Component Specifications

### Header (`header.scss`)
- **Background**: `rgba(15,16,17,0.75)` with `backdrop-filter: blur(24px)`
- **Border-bottom**: `1px solid rgba(255,255,255,0.08)`
- **No hover effect** on header bar itself
- **Menu panels**: `background: #191a1b`, `border: 1px solid rgba(255,255,255,0.1)`, `border-radius: 8px`, `box-shadow: 0 4px 12px rgba(0,0,0,0.3)`
- **Header icons**: `background: rgba(255,255,255,0.05)`, `border-radius: 50%`
- **Logo**: Existing yoDEV logo component, no changes

### Topic List (`topic-list.scss`)
- **Layout**: Borderless list items separated by `1px solid rgba(255,255,255,0.05)` bottom borders
- **Topic card** (`.custom-topic-layout`):
  - Background: `transparent` (no card background)
  - No border, no shadow, no border-radius on the card itself
  - Grid: `grid-template-columns: 1fr 200px` (content + thumbnail)
  - Padding: `20px 0`
  - Hover: background shifts to `rgba(255,255,255,0.02)`, no transform
  - Selected: `box-shadow: inset 3px 0 0 var(--linear-accent)`
- **Metadata**: `font-size: 11px`, `color: var(--linear-text-tertiary)`, `font-weight: 510`
- **Title**: `font-size: 16px` (var(--font-up-1)), `font-weight: 510`, `color: var(--linear-text-primary)`
- **Excerpt**: `font-size: 14px`, `color: var(--linear-text-tertiary)`, `line-height: 1.5`
- **Thumbnail image**: `border-radius: 8px`, `width: 200px`, `height: 140px`, `object-fit: cover`
- **Bottom bar**: `font-size: 12px`, `color: var(--linear-text-quaternary)`, `font-weight: 510`
- **Visited state**: title and excerpt shift to `var(--linear-text-quaternary)`
- **Navigation bar** (`#navigation-bar`):
  - Contained pill group: `background: rgba(255,255,255,0.02)`, `border: 1px solid rgba(255,255,255,0.05)`, `border-radius: 8px`, `padding: 4px`
  - Active pill: `background: rgba(94,106,210,0.15)`, `color: var(--linear-text-primary)`
  - Inactive pills: `color: var(--linear-text-tertiary)`, no background
  - Pill radius: `6px`

### Topic View (`topic.scss`)
- **Topic title bar**: `background: var(--linear-surface)`, `border-radius: 8px 8px 0 0`, `padding: 1em 2em`
- **Title text**: `color: var(--linear-text-primary)`, normal weight (not bold)
- **Posts wrapper**: `background: rgba(255,255,255,0.02)`, `border: 1px solid rgba(255,255,255,0.08)`, `border-radius: 0 0 8px 8px`
- **Timeline handle**: `background: var(--linear-accent)`
- **Topic map**: Linear card treatment (translucent bg, subtle border, 8px radius)

### Composer (`composer.scss`)
- **Background**: `#0f1011` (panel-dark)
- **Border**: `1px solid rgba(255,255,255,0.1)`
- **Border-radius**: `12px 12px 0 0` (top corners only)
- **Shadow**: `0 -4px 12px rgba(0,0,0,0.2)` (upward shadow since it anchors to bottom)
- **Hover**: border brightens to `rgba(255,255,255,0.15)`
- **Textarea wrapper**: `border: 1px solid rgba(255,255,255,0.08)`, `border-radius: 6px`
- **Editor toolbar buttons**: `background: rgba(255,255,255,0.05)`, `border-radius: 4px`
- **Grippie**: thin line, `border-color: rgba(255,255,255,0.1)`

### Right Sidebar (`right-sidebar.scss`)
- **Container**: No background, no border on the wrapper itself
- **Cards** (welcome, recent, category-about, tag-about):
  - `background: rgba(255,255,255,0.02)`
  - `border: 1px solid rgba(255,255,255,0.08)`
  - `border-radius: 8px`
  - `padding: 16px`
  - Hover: border to `rgba(255,255,255,0.12)`, no transform
- **Section headings**: `font-size: 11px`, `text-transform: uppercase`, `letter-spacing: 0.05em`, `color: var(--linear-text-quaternary)`, `font-weight: 590`
- **Recent topics**: Grid with image thumbnail, title clamped to 2 lines
- **Sticky behavior**: `position: sticky`, `top: calc(var(--header-offset) + 1.5em)`

### Buttons (`common.scss`)
- **Primary** (`.btn-primary`):
  - `background: #5e6ad2`, `color: white`, `font-weight: 510`
  - `border-radius: 6px`, `padding: 7px 16px`
  - `box-shadow: 0 1px 3px rgba(94,106,210,0.3)` (subtle glow)
  - Hover: `background: #828fff`
- **Ghost/Default** (`.btn-default`, `.btn`):
  - `background: rgba(255,255,255,0.05)`
  - `border: 1px solid rgba(255,255,255,0.1)`
  - `color: var(--linear-text-secondary)`, `font-weight: 510`
  - `border-radius: 6px`
  - Hover: `background: rgba(255,255,255,0.08)`, `border-color: var(--linear-accent-bright)`
- **No transform on hover** — buttons don't translate or scale

### Scrollbar
- Width: `8px`
- Track: `transparent`
- Thumb: `rgba(255,255,255,0.1)`, `border-radius: 9999px`
- Thumb hover: `rgba(255,255,255,0.2)`

### Category Banner (`category-banner.scss`)
- Banner background uses category color
- Meta bar: `background: var(--linear-panel)`
- Title: Inter Variable, `font-weight: 510`, centered
- Clean, no heavy borders

### Category Boxes (`category-boxes.scss`)
- Linear card treatment: translucent background, subtle border, 8px radius
- Left border accent with `var(--category-badge-color)` at `3px` width
- Hover: border highlights to accent color

### Search (`search.scss`)
- Container: Linear card treatment
- Input fields: `background: rgba(255,255,255,0.03)`, `border-radius: 6px`
- Results: clean list, no heavy separators

### User Pages (`user.scss`)
- Profile card: Linear card treatment
- Nav pills: `border-radius: 6px`
- Content padding: `1em 2em 2em`

### Groups (`groups.scss`)
- Group boxes: `border-radius: 8px`, Linear card treatment
- Navigation: Linear card with clean borders

### Badges (`badges.scss`)
- Badge card: Linear card treatment

### Select Kit (`select-kit.scss`)
- Dropdown body: `border-radius: 6px`
- Hover: border highlights to accent

### Chat (`chat.scss`)
- Full page chat: `background: var(--linear-panel)`
- Hide right sidebar

## Global Styles (`common.scss`)

### Body
- `background: var(--linear-bg)` (`#08090a`)
- `font-family: Inter Variable` with fallbacks
- `font-feature-settings: "cv01", "ss03"`
- `line-height: 1.6`

### Headings
- `font-weight: 510`
- `letter-spacing: -0.02em`
- `font-feature-settings: "cv01", "ss03"`

### Focus styles
- `outline: 2px solid var(--linear-accent-bright)`
- `outline-offset: 2px`

### CSS custom properties
- `--d-button-border-radius: 6px`
- `--d-nav-pill-border-radius: 6px`
- `--d-input-border-radius: 6px`

### Mixins
- `@mixin shadow-linear` — `0 0 0 1px rgba(0,0,0,0.2)` ring shadow
- `@mixin shadow-linear-hover` — ring + `0 4px 12px rgba(0,0,0,0.2)` elevation
- `@mixin shadow-linear-sm` — `0 1px 3px rgba(0,0,0,0.1)` subtle
- `@mixin linear-card` — replaces `custom-card`. Translucent bg, subtle border, 8px radius, hover brightens border only (no transform)
- `@mixin custom-topic-width` — unchanged from current

### Grid Layout
- Main content area: `max-width: 640px`
- Grid with sidebar, content, sidebar2 areas — unchanged structure
- Sidebar border: `1px solid rgba(255,255,255,0.05)`, hover to `rgba(255,255,255,0.08)`

## Mobile (`mobile.scss`)

- Topic card thumbnail: hidden on mobile, full-width layout
- Sidebar: hidden, navigation controls as floating button
- Composer: full-width, reduced padding
- Navigation bar: centered pills
- Category/tag banners: full-width
- Touch targets: adequate padding on all interactive elements

## Color Scheme (`about.json`)

Ships with "Linear Dark" scheme (already configured):
```json
{
  "is_dark": true,
  "primary": "f7f8f8",
  "secondary": "08090a",
  "tertiary": "5e6ad2",
  "quaternary": "7170ff",
  "header_background": "0f1011",
  "header_primary": "f7f8f8",
  "highlight": "5e6ad2",
  "danger": "e45735",
  "success": "27a644",
  "love": "7170ff",
  "primary_low": "191a1b",
  "primary_medium": "62666d",
  "secondary_low": "28282c",
  "secondary_medium": "191a1b",
  "tertiary_low": "1a1b2e"
}
```

## Files to Modify

All changes are SCSS-only. No JavaScript/Glimmer component changes.

| File | Action | Scope |
|------|--------|-------|
| `common/color_definitions.scss` | Rewrite | Clean Linear CSS custom properties |
| `common/common.scss` | Rewrite | Font import, mixins, body, buttons, grid, scrollbar, typography |
| `scss/header.scss` | Rewrite | Glass blur header, menu panels |
| `scss/topic-list.scss` | Rewrite | Borderless list layout, nav pills, dismiss buttons |
| `scss/topic.scss` | Rewrite | Topic title, posts wrapper, timeline |
| `scss/composer.scss` | Rewrite | Panel-dark composer with subtle depth |
| `scss/right-sidebar.scss` | Rewrite | Linear card sections, sticky behavior |
| `scss/categories.scss` | Rewrite | Category list cards |
| `scss/category-banner.scss` | Minor update | Clean up borders/radius |
| `scss/category-boxes.scss` | Rewrite | Linear card boxes |
| `scss/search.scss` | Rewrite | Clean search container |
| `scss/select-kit.scss` | Minor update | Radius and hover |
| `scss/tag.scss` | Minor update | Radius and card treatment |
| `scss/user.scss` | Minor update | Radius and card treatment |
| `scss/groups.scss` | Minor update | Radius and card treatment |
| `scss/badges.scss` | Minor update | Card treatment |
| `scss/chat.scss` | No change | Already minimal |
| `mobile/mobile.scss` | Rewrite | Mobile-specific overrides |
| `about.json` | No change | Color scheme already set |

## What Stays the Same

- All JavaScript/Glimmer components (structure, data fetching, interactions)
- `about.json` configuration (modifiers, components, color scheme)
- `settings.yml`
- `locales/en.yml`
- Grid layout structure (sidebar + content + sidebar2)
- Outlet connector architecture
