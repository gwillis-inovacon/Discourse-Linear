# Linear-Native Discourse Theme Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rewrite all SCSS in the Discourse-Linear theme to achieve a native Linear aesthetic — borderless topic list, glass blur header, subtle depth interactive elements, luminance-stepped surfaces.

**Architecture:** Pure SCSS rewrite across 17 files. No JavaScript/Glimmer changes. The `linear-card` mixin replaces `custom-card` as the foundational surface treatment. All components use CSS custom properties defined in `color_definitions.scss`. The color scheme in `about.json` is already correct.

**Tech Stack:** SCSS, Discourse theme architecture, CSS custom properties, CSS Grid

---

### Task 1: Foundation — Color Definitions

**Files:**
- Rewrite: `common/color_definitions.scss`

- [ ] **Step 1: Rewrite color_definitions.scss**

```scss
// Linear-native color definitions
// No light-mode support — this is a dark-mode-only theme

:root {
  // Surface hierarchy (luminance stepping)
  --linear-bg: #08090a;
  --linear-panel: #0f1011;
  --linear-surface: #191a1b;
  --linear-surface-secondary: #28282c;

  // Text hierarchy
  --linear-text-primary: #f7f8f8;
  --linear-text-secondary: #d0d6e0;
  --linear-text-tertiary: #8a8f98;
  --linear-text-quaternary: #62666d;

  // Brand accent
  --linear-accent: #5e6ad2;
  --linear-accent-bright: #7170ff;
  --linear-accent-hover: #828fff;

  // Borders
  --linear-border-default: rgba(255, 255, 255, 0.05);
  --linear-border-standard: rgba(255, 255, 255, 0.08);
  --linear-border-emphasis: rgba(255, 255, 255, 0.12);

  // Shadows
  --linear-shadow-ring: 0 0 0 1px rgba(0, 0, 0, 0.2);
  --linear-shadow-subtle: 0 1px 3px rgba(0, 0, 0, 0.2);
  --linear-shadow-panel: 0 4px 12px rgba(0, 0, 0, 0.2);

  // Legacy compatibility (used by Discourse internals)
  --redditish-bg-color: #08090a;
  --redditish-border-color: rgba(255, 255, 255, 0.08);
  --redditish-border-highlight-color: #7170ff;
}
```

- [ ] **Step 2: Verify file saved correctly**

Run: `cat common/color_definitions.scss | head -5`
Expected: First lines match the file above

- [ ] **Step 3: Commit**

```bash
git add common/color_definitions.scss
git commit -m "feat: rewrite color definitions with Linear design tokens"
```

---

### Task 2: Foundation — Common Styles, Mixins, Body, Buttons, Grid, Typography

**Files:**
- Rewrite: `common/common.scss`

- [ ] **Step 1: Rewrite common.scss**

```scss
@import url('https://rsms.me/inter/inter.css');

:root {
  --d-button-border-radius: 6px;
  --d-nav-pill-border-radius: 6px;
  --d-input-border-radius: 6px;
  --topic-body-width: 900px;
}

// ── Mixins ──

@mixin shadow-linear {
  box-shadow: var(--linear-shadow-ring);
}

@mixin shadow-linear-hover {
  box-shadow: var(--linear-shadow-ring), var(--linear-shadow-panel);
}

@mixin shadow-linear-sm {
  box-shadow: var(--linear-shadow-subtle);
}

@mixin linear-card {
  box-sizing: border-box;
  padding: 16px;
  border-radius: 8px;
  border: 1px solid var(--linear-border-standard);
  background: rgba(255, 255, 255, 0.02);
  transition: border-color 0.2s ease;

  @content;

  .discourse-no-touch & {
    &:hover {
      border-color: var(--linear-border-emphasis);
    }
  }
}

@mixin custom-topic-width {
  width: calc(100% - 8em);

  @media screen and (width <= 924px) {
    width: 100%;
  }
}

// ── Imports ──

@import "badges";
@import "categories";
@import "category-banner";
@import "category-boxes";
@import "chat";
@import "composer";
@import "groups";
@import "header";
@import "right-sidebar";
@import "search";
@import "select-kit";
@import "tag";
@import "topic";
@import "topic-list";
@import "user";

// ── Body ──

body {
  background: var(--linear-bg);
  background-image: none !important;
  font-family: 'Inter', 'Inter Variable', -apple-system, system-ui,
    'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans',
    'Helvetica Neue', sans-serif;
  font-feature-settings: "cv01", "ss03";
  line-height: 1.6;

  &:not(.has-sidebar-page) {
    .sidebar-wrapper {
      background: var(--linear-bg);
    }
  }

  &.tags-page,
  &[class*="navigation-"]:not([class*="archetype-"]),
  &[class*="category-"]:not([class*="archetype-"]) {
    #main-outlet-wrapper {
      grid-template-areas:
        "category-banner category-banner category-banner category-banner category-banner category-banner"
        "sidebar lspace content sidebar2 rspace sidebar-spacer";
      grid-template-columns:
        var(--d-sidebar-width) 1fr minmax(0, 640px)
        312px 1fr var(--d-sidebar-width);
      grid-template-rows: auto 1fr;
      gap: 0 1.5em;

      @media screen and (width <= 1500px) {
        grid-template-columns: auto 1fr minmax(0, 640px) 312px 1fr auto;
      }

      @media screen and (width <= 1160px) {
        grid-template-areas:
          "category-banner category-banner category-banner category-banner category-banner"
          "sidebar lspace content rspace sidebar-spacer";
        grid-template-columns: auto 1fr minmax(0, 640px) 1fr auto;

        .custom-right-sidebar {
          display: none;
        }
      }

      @media screen and (width <= 768px) {
        gap: 0.5em;
      }
    }
  }

  &.has-sidebar-page.tags-page,
  &.has-sidebar-page[class*="navigation-"]:not([class*="archetype-"]),
  &.has-sidebar-page[class*="category-"]:not([class*="archetype-"]) {
    #main-outlet-wrapper {
      grid-template-areas:
        "sidebar category-banner category-banner category-banner category-banner category-banner"
        "sidebar lspace content sidebar2 rspace sidebar-spacer";

      @media screen and (width <= 1160px) {
        grid-template-areas:
          "sidebar category-banner category-banner category-banner category-banner"
          "sidebar lspace content rspace sidebar-spacer";
        grid-template-columns: auto 1fr minmax(0, 640px) 1fr auto;

        .custom-right-sidebar {
          display: none;
        }
      }
    }
  }

  &[class*="user-"],
  &.archetype-regular {
    .custom-tag-banner,
    .custom-category-banner,
    .custom-right-sidebar {
      display: none;
    }
  }
}

// ── Buttons ──

.btn-default,
.btn-primary,
.btn {
  &:not(
      .btn-icon,
      [class*="popup-menu-btn"],
      [class*="post-action-menu__"],
      [class*="topic-map__"],
      .sidebar-section-link-wrapper &,
      .sidebar-section-link
    ) {
    padding: 7px 16px;
    border: 1px solid var(--linear-border-standard);
    border-radius: 6px;
    background: rgba(255, 255, 255, 0.05);
    color: var(--linear-text-secondary);
    font-weight: 510;
    transition: all 0.2s ease;

    &:hover {
      background: rgba(255, 255, 255, 0.08);
      border-color: var(--linear-accent-bright);
    }
  }
}

.btn-primary:not(
    .btn-icon,
    [class*="popup-menu-btn"],
    [class*="post-action-menu__"],
    [class*="topic-map__"],
    .sidebar-section-link-wrapper &,
    .sidebar-section-link
  ) {
  background: var(--linear-accent);
  color: #ffffff;
  border-color: transparent;
  box-shadow: 0 1px 3px rgba(94, 106, 210, 0.3);

  &:hover {
    background: var(--linear-accent-hover);
    border-color: transparent;
  }
}

.btn[class*="popup-menu-btn"] {
  border: none;
}

.dropdown-menu {
  .btn {
    border-radius: 6px;
  }
}

// ── Layout ──

#main-outlet {
  max-width: 640px;
  margin: 0 auto;
  grid-area: content;
  padding-bottom: 2em;
}

#banner {
  @include linear-card;
}

// ── Sidebar ──

.sidebar-wrapper .sidebar-container {
  border-right: 1px solid var(--linear-border-default);

  &:hover {
    border-color: var(--linear-border-standard);
  }
}

.sidebar-section-wrapper
  .sidebar-section-header.sidebar-section-header-collapsable {
  padding-left: 0;
}

.sidebar-section-link-wrapper button {
  border-radius: 6px;
  padding: 0 var(--d-sidebar-row-horizontal-padding) !important;
}

.sidebar-section-link-wrapper a.sidebar-section-link,
.wekan-sidebar-btn,
[class*="-sidebar-btn"] {
  display: flex !important;
  align-items: center;
  opacity: 1 !important;
  visibility: visible !important;
  border: none !important;
  box-shadow: none !important;
  transform: none !important;
  padding: 0.5em var(--d-sidebar-row-horizontal-padding) !important;
}

.add-to-sidebar {
  .d-icon-star {
    color: var(--tertiary);
  }
}

.admin-content {
  @include linear-card;
}

.body-page {
  @include linear-card;
}

.powered-by-discourse {
  border: 1px solid var(--linear-border-default);
  grid-area: content;
  align-self: end;
  border-radius: 6px;
}

html.composer-open #main-outlet {
  padding-bottom: var(--composer-height);
}

// ── Scrollbar ──

::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: transparent;
}

::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 9999px;
}

::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.2);
}

// ── Typography ──

h1, h2, h3, h4, h5, h6 {
  font-weight: 510;
  letter-spacing: -0.02em;
  font-feature-settings: "cv01", "ss03";
}

// ── Focus ──

button:focus-visible, a:focus-visible {
  outline: 2px solid var(--linear-accent-bright);
  outline-offset: 2px;
}
```

- [ ] **Step 2: Commit**

```bash
git add common/common.scss
git commit -m "feat: rewrite common.scss — Inter font, linear-card mixin, buttons, grid, scrollbar"
```

---

### Task 3: Header — Glass Blur

**Files:**
- Rewrite: `scss/header.scss`

- [ ] **Step 1: Rewrite header.scss**

```scss
.d-header {
  box-shadow: none;
  background: rgba(15, 16, 17, 0.75);
  backdrop-filter: blur(24px);
  -webkit-backdrop-filter: blur(24px);
  border-bottom: 1px solid var(--linear-border-standard);
}

.menu-panel {
  border-radius: 8px;
  border: 1px solid rgba(255, 255, 255, 0.1);
  background: var(--linear-surface);
  box-shadow: var(--linear-shadow-panel);
}

.user-menu.revamped .menu-tabs-container {
  padding: 0.5em;
  font-size: var(--font-down-1);

  .top-tabs {
    padding-bottom: 0.5em;
    margin-top: 0.5em;
  }

  .bottom-tabs {
    padding-top: 0.5em;
  }
}

.d-header-icons .icon {
  padding: 0.25em !important;
  border-radius: 50%;
  margin-left: 0.5em;
  background: rgba(255, 255, 255, 0.05);

  .d-icon {
    font-size: var(--font-up-2);
  }
}

.d-header-icons .active .icon {
  border-color: transparent !important;
}

.header-sidebar-toggle button .d-icon {
  font-size: var(--font-up-2);
  color: var(--linear-text-tertiary);
}
```

- [ ] **Step 2: Commit**

```bash
git add scss/header.scss
git commit -m "feat: glass blur header with Linear menu panels"
```

---

### Task 4: Topic List — Borderless Layout with Thumbnails

**Files:**
- Rewrite: `scss/topic-list.scss`

- [ ] **Step 1: Rewrite topic-list.scss**

```scss
body.tags-page,
body.category,
.navigation-topics,
.categories-list {
  .navigation-controls {
    position: fixed;
    right: 0;
    top: calc(var(--header-offset) + 1em);
    z-index: z("header") - 1;

    #create-topic,
    .select-kit.single-select.dropdown-select-box.notifications-button,
    .topic-drafts-menu-trigger,
    .notifications-tracking-trigger {
      display: none;
    }
  }
}

.topic-above-suggested-outlet,
.related-messages-wrapper,
.suggested-topics-wrapper {
  @include custom-topic-width;
}

#suggested-topics-title {
  font-weight: 510;
}

.category-breadcrumb {
  display: none;
}

.topic-list-bottom .footer-message {
  text-align: center;
  @include linear-card {
    padding: 2.5em 3em 2em;
  }
}

.category-heading {
  display: none;
}

// ── Navigation Bar — Contained pill group ──

#navigation-bar {
  width: 100%;
  margin: 0 0 1em 0;
  background: rgba(255, 255, 255, 0.02);
  border: 1px solid var(--linear-border-default);
  border-radius: 8px;
  padding: 4px;
  display: flex;

  li {
    a {
      border-radius: 6px;
      font-weight: 510;
      font-size: var(--font-down-1);
      color: var(--linear-text-tertiary);
      padding: 5px 12px;
      transition: all 0.15s ease;

      &:hover {
        color: var(--linear-text-primary);
      }
    }

    &.active a {
      background: rgba(94, 106, 210, 0.15);
      color: var(--linear-text-primary);
    }
  }
}

.list-controls {
  display: flex;
  flex-direction: column;
  gap: 1em;
}

div.education {
  border: none;
  text-align: left;
  padding: 0;

  + h3 {
    text-align: left;
  }

  p {
    margin-top: 0;
  }
}

.period-chooser-header {
  border-radius: 6px;
}

.select-kit.dropdown-select-box.period-chooser .period-chooser-row {
  font-weight: 510;
}

.select-kit.dropdown-select-box.period-chooser .period-chooser-header {
  text-transform: none;
  font-size: var(--font-down-1);

  .d-icon {
    padding: 0;
  }

  .date-section {
    font-weight: 510;
  }

  h2.selected-name {
    .date-section {
      margin: 0;
    }

    .top-date-string {
      display: none;
    }
  }
}

.top-date-string {
  text-transform: none !important;
  color: var(--primary-high) !important;
}

.topic-list-bottom {
  margin-top: 0;
}

// ── Topic List — Borderless items ──

.topic-list {
  display: flex;

  .topic-list-item {
    &.visited {
      .link-top-line a.title,
      .topic-excerpt {
        color: var(--linear-text-quaternary);
      }
    }

    &.selected {
      .custom-topic-layout {
        box-shadow: inset 3px 0 0 var(--linear-accent);
      }
    }
  }

  a.title {
    overflow-wrap: break-word;
  }

  .badge-notification,
  .category-topic-link td.num .badge-notification {
    font-size: var(--font-down-3);
    top: -2px;
    padding: 0.2em 0.4em 0.2em;
  }

  .topic-list-item-separator {
    .topic-list-data {
      display: flex;
      justify-content: center;
      background: var(--danger-medium);
      max-width: unset;

      span {
        position: absolute;
        margin-top: -0.33em;
        background: var(--linear-bg);
        padding: 0.25em;
      }
    }
  }

  tbody {
    border: none;
    display: flex;
    flex-direction: column;
    min-width: 0;
  }

  tr {
    border: none;
  }
}

// ── Topic Card — Borderless with right thumbnail ──

.custom-topic-layout {
  cursor: pointer;
  width: 100%;
  box-sizing: border-box;
  padding: 20px 0;
  border: none;
  border-bottom: 1px solid var(--linear-border-default);
  border-radius: 0;
  background: transparent;
  box-shadow: none;
  transition: background 0.15s ease;

  .discourse-no-touch & {
    &:hover {
      background: rgba(255, 255, 255, 0.02);
      transform: none;
      box-shadow: none;
    }
  }

  h2 {
    margin: 0 !important;
    font-weight: 510;
    font-size: var(--font-up-1);
    color: var(--linear-text-primary);
  }

  &_meta {
    color: var(--linear-text-tertiary);
    margin-bottom: 0.5em;
    padding-left: 0.05em;
    min-width: 0;
    font-size: var(--font-down-1);
    font-weight: 510;

    a:not(.badge-wrapper) {
      color: currentcolor;

      &:hover,
      &:focus {
        text-decoration: underline;
        color: var(--linear-accent-bright);
      }
    }

    &-posted {
      font-size: var(--font-down-1);
    }

    .badge-wrapper {
      margin-right: 0;
    }
  }

  &_excerpt {
    margin: 0.5em 0 1em;
    font-size: var(--font-0);
    color: var(--linear-text-tertiary);
    line-height: 1.5;

    .topic-excerpt-more {
      font-size: var(--font-down-1);
    }
  }

  &_image {
    width: 200px;
    height: 140px;
    overflow: hidden;
    margin: 0;
    flex-shrink: 0;

    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      border-radius: 8px;
    }
  }

  &_bottom-bar {
    font-size: var(--font-down-1);
    color: var(--linear-text-quaternary);
    padding: 0.25em 0;
    font-weight: 510;

    span {
      margin-right: 0.5em;
    }
  }
}

// ── Post bar ──

.custom-post-bar:empty {
  display: none;
}

.custom-post-bar-contents {
  display: flex;
  align-items: center;
  @include linear-card;

  .avatar {
    margin: 0 1em 0 0;
    width: 38px;
    height: 38px;
  }

  input {
    width: 100%;
    margin: 0;
    background: rgba(255, 255, 255, 0.03);
    border: 1px solid var(--linear-border-standard);
    border-radius: 6px;

    &:hover {
      border-color: var(--linear-accent-bright);
    }
  }

  .topic-drafts-menu-trigger {
    margin-left: 0.5em;
  }
}

// ── Dismiss buttons ──

.topic-list-bottom .dismiss-container-bottom {
  float: none;
  margin-bottom: 1em;
}

#dismiss-topics-top,
#dismiss-new-top,
#dismiss-new-bottom,
#dismiss-topics-bottom {
  display: block;
  background: rgba(255, 255, 255, 0.02);
  border: 1px solid var(--linear-border-standard);
  border-radius: 6px;

  .discourse-no-touch & {
    &:hover {
      border-color: var(--linear-accent-bright);
      color: var(--primary);

      .d-icon {
        color: currentcolor;
      }
    }
  }
}

.dismiss-container-top {
  justify-content: flex-start;
}

#dismiss-topics-top,
#dismiss-new-top {
  margin-bottom: 0.5em;
}

// ── Category boxes ──

.category-boxes {
  .description,
  h3 {
    text-align: left;
  }

  .description {
    margin-top: 0.5em;
  }

  .subcategories {
    margin-left: -0.5em;
  }
}

.d-icon-undefined {
  display: none;
}

.category-boxes .category-box,
.category-boxes-with-topics .category-box {
  .category-logo.aspect-image img {
    margin-left: 0;
  }
}
```

- [ ] **Step 2: Commit**

```bash
git add scss/topic-list.scss
git commit -m "feat: borderless topic list with contained pill nav"
```

---

### Task 5: Topic View

**Files:**
- Rewrite: `scss/topic.scss`

- [ ] **Step 1: Rewrite topic.scss**

```scss
#topic-title {
  background: var(--linear-surface);
  margin: 0;
  border-radius: 8px 8px 0 0;
  box-sizing: border-box;
  padding: 1em 2em;

  @include custom-topic-width;

  h1 {
    font-size: var(--font-up-4);
    font-weight: 510;
    color: var(--secondary);

    a {
      color: var(--secondary);
    }

    margin-bottom: 0.25em;
  }

  .badge-category__name,
  .discourse-tag.simple,
  .discourse-tag.simple:visited,
  .discourse-tag.simple:hover {
    color: var(--secondary);
  }

  a.edit-topic {
    font-size: var(--font-down-2);
    vertical-align: 0.15em;
  }

  .edit-topic-title {
    .category-name,
    .discourse-tag.simple {
      color: var(--primary);
    }

    .select-kit.multi-select {
      background: transparent;
    }
  }
}

.signup-cta {
  @include linear-card;
  @include custom-topic-width;

  .buttons {
    margin-bottom: 0;
  }
}

.topic-post.topic-owner {
  .topic-avatar,
  .topic-body {
    border-top: none;
  }
}

.names span.first {
  font-weight: 510;
}

.topic-list-header {
  display: none;
}

.topic-list-body {
  width: 100%;
}

.timeline-container.timeline-docked-bottom .timeline-footer-controls {
  opacity: 1;
  pointer-events: all;
}

.timeline-container .topic-timeline .timeline-scrollarea {
  border-color: var(--linear-text-quaternary);
}

.timeline-container .topic-timeline .timeline-handle {
  background-color: var(--linear-accent);
}

.timeline-footer-controls,
.timeline-controls,
.topic-notifications-button,
.topic-admin-menu-button {
  > button,
  .topic-notifications-button summary {
    height: 2.6em;
    font-size: var(--font-down-1);

    @include linear-card {
      padding-top: 0.6em;
      padding-bottom: 0.65em;
    }
  }
}

.topic-admin-popup-menu {
  font-size: var(--font-up-1);
}

#topic-footer-buttons {
  margin-bottom: 0;

  @include custom-topic-width;

  .btn-primary {
    float: none;
  }

  @if $hide_topic_footer_controls == "true" {
    .topic-footer-main-buttons {
      display: none;
    }
  } @else {
    .topic-footer-main-buttons {
      .btn-default {
        background-color: rgba(255, 255, 255, 0.03);
        border: 1px solid var(--linear-border-standard);

        .discourse-no-touch & {
          &:hover {
            background-color: rgba(255, 255, 255, 0.08);
          }
        }
      }
    }
  }

  .select-kit.single-select.dropdown-select-box .select-kit-header {
    background: rgba(255, 255, 255, 0.03);
    border-color: var(--linear-border-standard);

    &:hover,
    &:focus {
      background: rgba(255, 255, 255, 0.06);
      color: var(--primary);

      .d-icon {
        color: inherit;
      }

      .d-icon.d-icon-d-tracking,
      .d-icon.d-icon-d-watching {
        color: var(--linear-accent);
      }
    }
  }

  .pinned-button .reason .text,
  .topic-notifications-button .reason .text {
    margin-left: 1em;
    color: var(--primary);
  }
}

.timeline-container .topic-timeline {
  .timeline-ago,
  .start-date,
  .now-date {
    color: var(--linear-text-quaternary);
  }

  timeline-replies {
    font-weight: 510;
  }
}

.timeline-footer-controls {
  flex-direction: column;
}

.topic-list-bottom {
  h3 {
    font-weight: 510;
    line-height: 1.2;
  }

  .top-title-buttons {
    margin-top: 2em;
    display: flex;
    justify-content: center;
  }
}

.container.posts {
  grid-template-columns: 1fr 8em;

  @media screen and (width <= 924px) {
    grid-template-columns: 1fr;
  }

  #topic-progress-wrapper #topic-progress {
    border: 1px solid var(--linear-border-standard);

    .nums {
      top: 0.1em;
    }
  }

  #topic-progress-wrapper {
    --d-border-radius: 100px;

    .topic-admin-menu-button .toggle-admin-menu {
      border-right: none;
      border-radius: var(--d-border-radius) 0 0 var(--d-border-radius);
      height: 100%;
    }
  }
}

.posts-wrapper {
  @include linear-card {
    border-radius: 0 0 8px 8px;
    border-top: none !important;
    padding: 1em 2em 2em;
  }
}

.topic-map.--bottom {
  margin-top: 1em;
  max-width: unset;

  @include linear-card;
}

.map:first-of-type .buttons .btn {
  border-radius: 6px;
}

h3.suggested-topics-message {
  font-weight: 510;
}

.more-topics__container .nav {
  position: relative;
}

html:not(.footer-nav-visible) #topic-progress-wrapper {
  bottom: calc(env(safe-area-inset-bottom) + 1em);
}
```

- [ ] **Step 2: Commit**

```bash
git add scss/topic.scss
git commit -m "feat: Linear topic view — surface title bar, translucent posts"
```

---

### Task 6: Composer — Panel-dark with Subtle Depth

**Files:**
- Rewrite: `scss/composer.scss`

- [ ] **Step 1: Rewrite composer.scss**

```scss
#reply-control {
  border-radius: 12px 12px 0 0;
  border: 1px solid rgba(255, 255, 255, 0.1);
  background: var(--linear-panel);
  box-shadow: 0 -4px 12px rgba(0, 0, 0, 0.2);

  .composer-fields:empty {
    display: none;
  }

  .d-editor-textarea-wrapper,
  .select-kit.combo-box .select-kit-header,
  .select-kit.multi-select .multi-select-header,
  #reply-title {
    border-color: var(--linear-border-standard);
    border-radius: 6px;
    border-width: 1px;
  }

  .d-editor-textarea-wrapper {
    margin-top: 0.5em;
  }

  &:hover {
    border-color: rgba(255, 255, 255, 0.15);
  }

  .grippie {
    background: none;
    margin-top: -1em;
    display: inline-block;

    &::before {
      margin-left: calc(50% - 2.25em);
      padding: 0.75em 1em;
      margin-right: calc(50% - 1.85em);
      border-top-color: rgba(255, 255, 255, 0.1);
      border-top-width: 1px;
    }

    &:hover {
      &::before {
        border-top-color: var(--linear-accent-bright);
      }
    }
  }

  .reply-area {
    padding: 0 1.75em 0.5em;
    height: 100%;
  }

  .reply-to {
    margin-bottom: 1em;

    .composer-action-title {
      .topic-link,
      .user-link,
      .post-link {
        color: var(--primary-high);
      }
    }
  }

  .submit-panel {
    margin-top: 1em;
  }

  .d-editor-preview-wrapper {
    margin-left: 2em;
  }

  .reply-details {
    .select-kit.dropdown-select-box.composer-actions {
      .select-kit-header {
        background: rgba(255, 255, 255, 0.05);
        border-color: transparent;
        transition: none;
        padding: 0.05em 0;
        margin-right: 0.5em;

        &:hover {
          background: rgba(255, 255, 255, 0.1);

          .d-icon {
            color: var(--secondary);
          }
        }
      }
    }
  }
}

.d-editor-button-bar {
  border: none;
  margin: 0.25em 0.25em 0;
  grid-template-columns: repeat(auto-fill, minmax(2em, 1fr));
  gap: 0.15em;

  .btn {
    padding: 0.4em 0 0.33em;
    background: rgba(255, 255, 255, 0.05);
    border-radius: 4px;
  }
}

.action-title {
  font-size: var(--font-up-1);
  color: var(--primary-high);

  .avatar {
    transform: scale(75%);
  }
}

#reply-control:not(.fullscreen).hide-preview {
  --topic-body-width: 690px;
}
```

- [ ] **Step 2: Commit**

```bash
git add scss/composer.scss
git commit -m "feat: panel-dark composer with subtle depth and upward shadow"
```

---

### Task 7: Right Sidebar

**Files:**
- Rewrite: `scss/right-sidebar.scss`

- [ ] **Step 1: Rewrite right-sidebar.scss**

The right-sidebar.scss is the second-largest file (199 lines). The structure stays identical — we're only changing the visual treatment from custom-card to linear-card, removing transforms on hover.

```scss
.custom-right-sidebar {
  display: flex;
  flex-direction: column;
  gap: 1em;
  margin-top: 1.5em;
  grid-area: sidebar2;

  @media screen and (height >= 900px) {
    position: sticky;
    top: calc(var(--header-offset) + 1.5em);
    align-self: start;
  }

  &_welcome,
  &_recent,
  &_category-about,
  &_tag-about {
    @include linear-card;
  }

  &_tag-about,
  &_category-about {
    p {
      color: var(--linear-text-tertiary);
      margin: 0;

      + .custom-right-sidebar_controls {
        margin-top: 1em;
      }
    }

    &.-tags-and-subcategories {
      display: flex;
      flex-direction: column;
      gap: 1em 0;
    }
  }

  &_welcome {
    p {
      margin-bottom: 0.25em;
    }

    button {
      width: 100%;
      margin: 0.5em 0;
    }
  }

  &_welcome + .custom-right-sidebar_controls {
    margin-top: 0.5em;
  }

  &_controls {
    display: flex;
    align-items: center;
    gap: 0.5em;

    #custom-create-topic {
      flex: 1 1 auto;
    }
  }

  &_tags {
    .discourse-tags {
      font-size: var(--font-down-1);
      display: flex;
      flex-wrap: wrap;
      gap: 0.25em;

      a {
        display: flex;
      }
    }
  }

  &_recent {
    &-topics-wrapper {
      border: none;
      border-top: 1px solid var(--linear-border-default);

      &:hover {
        border-color: var(--linear-border-emphasis);

        + .custom-right-sidebar_recent-topics-wrapper {
          border-color: var(--linear-border-emphasis);
        }
      }
    }

    h4 {
      font-size: var(--font-down-2);
      margin-top: 0.25em;
      text-transform: uppercase;
      letter-spacing: 0.05em;
      color: var(--linear-text-quaternary);
      font-weight: 590;
    }

    h2 {
      margin: 0.25em 0 !important;
      font-weight: 510;

      a {
        color: var(--primary);
      }
    }

    .visited {
      h2 {
        a {
          color: var(--linear-text-quaternary);
        }
      }
    }

    .custom-topic-layout {
      border: none !important;
      border-radius: 0;
      padding: 1em 0;
      background: transparent;
      box-shadow: none;

      .discourse-no-touch & {
        &:hover {
          transform: none;
          box-shadow: none;
        }
      }

      .link-bottom-line,
      .bullet-separator,
      .share-toggle,
      &_excerpt {
        display: none;
      }

      &_meta {
        display: flex;
        gap: 0.25em;
        margin-bottom: 0;
        padding-left: 0;

        .badge-wrapper {
          display: none;
        }

        [data-user-card] {
          display: none;
        }

        &-posted {
          min-width: 0;
          display: flex;
          flex: 1 1 auto;
          gap: 0.5em;
        }

        &-posted-by {
          display: none;
        }
      }

      .link-top-line {
        font-size: var(--font-0);
      }
    }

    &-topics {
      display: flex;
      flex-direction: column;

      .custom-topic-layout {
        display: grid;
        grid-template-areas: "image content1" "image content2" "image content3";
        grid-template-columns: auto 1fr;
        grid-template-rows: auto auto auto;
        gap: 0;

        &_meta {
          grid-area: content1;
        }

        .link-top-line {
          grid-area: content2;
          display: -webkit-box;
          -webkit-box-orient: vertical;
          -webkit-line-clamp: 2;
          overflow: hidden;
        }

        &_bottom-bar {
          grid-area: content3;
        }

        &_image {
          grid-area: image;
          width: 90px;
          height: 85px;
          overflow: hidden;
          margin: 0 0.5em 0 0;

          img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            border-radius: 8px;
          }
        }
      }
    }
  }
}
```

- [ ] **Step 2: Commit**

```bash
git add scss/right-sidebar.scss
git commit -m "feat: right sidebar with linear-card sections, no hover transforms"
```

---

### Task 8: Minor Components — Categories, Category Boxes, Category Banner, Search, Select Kit, Tag, User, Groups, Badges

**Files:**
- Rewrite: `scss/categories.scss`
- Rewrite: `scss/category-boxes.scss`
- Rewrite: `scss/category-banner.scss`
- Rewrite: `scss/search.scss`
- Rewrite: `scss/select-kit.scss`
- Rewrite: `scss/tag.scss`
- Rewrite: `scss/user.scss`
- Rewrite: `scss/groups.scss`
- Rewrite: `scss/badges.scss`
- No change: `scss/chat.scss`

- [ ] **Step 1: Rewrite categories.scss**

```scss
.categories-and-latest,
.categories-and-top,
.category-list {
  @include linear-card;
}

.categories-and-latest,
.categories-and-top {
  .category-list {
    border: none;

    .discourse-no-touch & {
      &:hover {
        border: none;
      }
    }
  }
}

.navigation-categories {
  #main-outlet-wrapper #main-outlet {
    grid-column-end: sidebar2;

    @media screen and (width <= 1160px) {
      grid-column-end: auto;
    }
  }

  .custom-right-sidebar {
    display: none;
  }

  .list-controls {
    gap: 0.25em;
  }

  #navigation-bar {
    display: none;
  }
}
```

- [ ] **Step 2: Rewrite category-boxes.scss**

```scss
.category-boxes,
.category-boxes-with-topics {
  grid-template-columns: repeat(auto-fit, minmax(20em, 1fr));

  .category-box {
    @include linear-card;

    & {
      border-top: 1px solid transparent;
      border-right: 1px solid transparent;
      border-bottom: 1px solid transparent;
      border-left: 3px solid var(--category-badge-color);
    }

    .discourse-no-touch & {
      &:hover {
        border-top: 1px solid var(--linear-border-emphasis);
        border-right: 1px solid var(--linear-border-emphasis);
        border-bottom: 1px solid var(--linear-border-emphasis);
        border-left: 3px solid var(--category-badge-color);
      }
    }

    .category-box-inner {
      border: none;
    }

    .subcategories .subcategory {
      margin-bottom: 0.25em;
    }
  }
}
```

- [ ] **Step 3: Rewrite category-banner.scss**

```scss
.custom-tag-banner,
.custom-category-banner {
  grid-area: category-banner;
  margin: 0 0 0 -1.5em;

  @media screen and (width <= 782px) {
    margin: 0;
  }

  .category-logo.aspect-image {
    float: none;
    margin: 0;
  }

  &_background {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 12em;

    .category-logo.aspect-image {
      --max-height: 100px;
    }
  }

  &_meta {
    background: var(--linear-panel);
  }

  &_meta-text {
    height: 6em;
    margin: 0 auto;
    padding: 1em 0 0.25em;
    display: flex;
    justify-content: center;
    align-items: center;
    color: var(--secondary);

    h1 {
      margin-top: -0.2em;
      text-align: center;
      padding: 0 0.33em;
      font-weight: 510;

      @include ellipsis;
    }

    a {
      color: var(--secondary);
    }
  }
}

.custom-tag-banner {
  h1 a {
    color: var(--primary-low);
    font-weight: 510;

    .d-icon {
      font-size: var(--font-down-3);
      color: var(--primary-low-mid);
    }
  }

  &_meta {
    background: var(--primary-high);
  }
}
```

- [ ] **Step 4: Rewrite search.scss**

```scss
.search-container {
  @include linear-card;

  .search-advanced .search-info,
  .search-advanced .search-results,
  .search-header {
    padding: 1em 2em;
    background: transparent;
  }

  .search-filters,
  .search-filters .search-advanced-filters {
    background: none;
  }
}

.floating-search-input .search-banner-inner.wrap .search-menu .results {
  border: none;
}

.search-menu .menu-panel,
.search-menu-container .menu-panel {
  border: 1px solid transparent;

  &:hover {
    border: 1px solid var(--linear-border-emphasis);
  }
}
```

- [ ] **Step 5: Rewrite select-kit.scss**

```scss
.select-kit.single-select.dropdown-select-box {
  .select-kit-row {
    .texts {
      .name {
        font-weight: 510;
      }
    }
  }

  &.is-expanded {
    .select-kit-collection,
    .select-kit-body {
      border-radius: 6px;
    }

    .select-kit-body {
      &:hover {
        border: 1px solid var(--linear-border-emphasis);
      }
    }
  }
}
```

- [ ] **Step 6: Rewrite tag.scss**

```scss
.tag-sort-options + hr {
  display: none;
}

.all-tag-lists {
  @include linear-card;

  .tags-list {
    margin: 0;
    border: none;
    padding: 0;
  }
}

section.tag-info {
  background: var(--tertiary-low);
  padding: 1em;
  border: none;
  border-radius: 8px;
  margin-top: 0;
}

.tags-page {
  #main-outlet {
    display: flex;
    flex-direction: column;
  }

  section.tag-info {
    order: -1;
  }
}
```

- [ ] **Step 7: Rewrite user.scss**

```scss
.user-main {
  @include linear-card;
}

.horizontal-overflow-nav {
  .nav-pills > li > a,
  .nav-pills > li button {
    border-radius: 6px;
  }
}

.user-content {
  padding: 1em 2em 2em;
}

.users-page {
  #main-outlet-wrapper #main-outlet {
    margin-top: 2em;

    @include linear-card;
  }
}
```

- [ ] **Step 8: Rewrite groups.scss**

```scss
.groups-boxes .group-box {
  border-radius: 8px;
  border: none;
}

.group-box-inner {
  @include linear-card;
}

.container.group {
  .user-primary-navigation,
  .user-secondary-navigation {
    background: rgba(255, 255, 255, 0.02);

    @include linear-card;
  }

  .user-secondary-navigation {
    margin-top: 0.5em;
  }

  .horizontal-overflow-nav {
    border: none;
  }
}
```

- [ ] **Step 9: Rewrite badges.scss**

```scss
.badge-card .badge-contents {
  height: 100%;

  @include linear-card;
}

.badge-card .favorite-btn {
  right: 0.5em;
  bottom: 0.5em;
}
```

- [ ] **Step 10: Commit all minor components**

```bash
git add scss/categories.scss scss/category-boxes.scss scss/category-banner.scss scss/search.scss scss/select-kit.scss scss/tag.scss scss/user.scss scss/groups.scss scss/badges.scss
git commit -m "feat: Linear card treatment for all minor components"
```

---

### Task 9: Mobile Overrides

**Files:**
- Rewrite: `mobile/mobile.scss`

- [ ] **Step 1: Rewrite mobile.scss**

```scss
#main-outlet-wrapper {
  gap: 0 !important;
}

#main-outlet,
.topic-list .topic-excerpt,
.navigation-toggle,
.navigation-toggle .toggle-link {
  width: 100%;
}

.navigation-toggle .toggle-link {
  justify-content: center;
}

.custom-post-bar {
  display: none;
}

// ── Topic cards — hide thumbnail on mobile ──

.custom-topic-layout {
  h2 {
    line-height: 1.2;

    a {
      color: var(--primary);
    }
  }

  .link-bottom-line {
    margin-top: 0.25em;
  }

  &_image {
    display: none;
  }
}

// ── Navigation ──

body.tags-page,
body.category,
.navigation-topics,
.categories-list {
  .navigation-controls {
    right: 1.25em;
    bottom: 1em;
    top: unset;

    .bulk-select {
      display: none;
    }

    #create-topic {
      display: flex !important;
      background: var(--linear-accent);
      color: #ffffff;
      box-shadow: var(--linear-shadow-panel);
      border-radius: 50%;

      .d-icon {
        color: #ffffff;
      }
    }
  }
}

// ── Page padding ──

#topic-title,
.posts-wrapper,
.topic-list-bottom .footer-message,
.search-container .search-advanced .search-info,
.search-container .search-advanced .search-results,
.search-container .search-header {
  padding: 1em 1.5em;
}

.posts-wrapper {
  padding-bottom: 1.5em;
}

.topic-owner {
  &.topic-post article {
    border: none;
  }
}

.navigation-categories #main-outlet .list-controls {
  display: block;
}

.list-controls .nav-pills .drop {
  right: 0;
  width: unset;
  box-shadow: var(--shadow-dropdown);
}

.nav-pills > li > a:hover,
.nav-pills > li button:hover {
  background: transparent;
  color: var(--primary);
}

.custom-tag-banner,
.custom-category-banner {
  width: calc(100% + 1.5em);
  margin-left: -0.75em;
}

.topic-list-bottom {
  margin-top: 1em;
}

.user-content {
  padding: 0;
}

.user-menu.revamped .tabs-list .btn {
  padding: 1em 1.4em;
}

.d-header-icons .icon {
  margin-left: 0;
  padding: 0 !important;
}

.body-page .nav-pills {
  font-size: var(--font-down-1);
}

// ── Topic footer ──

#topic-footer-buttons {
  margin-bottom: calc(env(safe-area-inset-bottom) + 1em);

  .topic-notifications-button .reason,
  .pinned-button .reason {
    .select-kit {
      .selected-name .name {
        display: none;
      }
    }

    .text {
      margin: 0.5em 0 0 0;
    }
  }

  &.docked {
    margin-top: 0.5em;
  }
}

.timeline-footer-controls,
.topic-admin-menu-button {
  button,
  .topic-notifications-button summary {
    height: unset;
  }
}

html:not(.footer-nav-visible) #topic-progress-wrapper {
  bottom: calc(env(safe-area-inset-bottom) + 1em);
}

#topic-progress {
  --d-border-radius: 100px;
  margin-right: 0.5em;
  margin-left: 0;
  border-radius: 100px;
  border: 1px solid var(--linear-border-standard);
  overflow: hidden;

  .nums {
    top: 0.5em;
  }
}

#topic-progress-wrapper {
  margin-bottom: 1em;

  #topic-progress .nums {
    position: static;
  }
}

.container.posts
  #topic-progress-wrapper
  .topic-admin-menu-button
  .toggle-admin-menu {
  border-right: none;
  border-radius: 100px 0 0 100px;
}

.category-boxes,
.category-boxes-with-topics {
  margin-top: 0;
}

// ── Composer ──

#reply-control {
  .reply-to {
    margin: 0.5em 0;
    font-size: var(--font-down-1);
  }

  .reply-area {
    padding: 0 0.5em 0.5em;
  }

  .grippie {
    display: none;
  }

  .submit-panel {
    margin-bottom: 0.25em;
    margin-top: 0.33em;
  }

  .composer-actions {
    .select-kit-body {
      font-size: var(--font-up-1);
    }
  }

  &.closed {
    border: none;
  }
}

.alert.alert-info.clickable {
  position: absolute;
  right: 0;
  left: 0;
}

.topic-list-bottom .top-title-buttons {
  margin-top: 1em;
  font-size: var(--font-down-2);
}

.custom-topic-layout_meta-posted-by {
  display: none;
}

#navigation-bar {
  justify-content: center;
}
```

- [ ] **Step 2: Commit**

```bash
git add mobile/mobile.scss
git commit -m "feat: mobile overrides — hidden thumbnails, FAB create button, Linear borders"
```

---

### Task 10: Final Verification and Push

- [ ] **Step 1: Verify all files are saved**

Run: `git status`
Expected: Clean working tree (all changes committed)

- [ ] **Step 2: Verify theme name in about.json**

Run: `grep '"name"' about.json`
Expected: `"name": "linear-inspired"`

- [ ] **Step 3: Verify color scheme in about.json**

Run: `grep -A2 '"Linear Dark"' about.json`
Expected: Shows the Linear Dark color scheme with `is_dark: true`

- [ ] **Step 4: Push all commits**

```bash
git push origin main
```

- [ ] **Step 5: Verify on GitHub**

Run: `gh repo view gwillis-inovacon/Discourse-Linear --web`
Expected: All commits visible, theme ready for installation
