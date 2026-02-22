# CLAUDE.md - Project Guide

## Project Overview

This is **Tatry Topo** — a Polish Tatra Mountains rock climbing route database, built as an **Adobe Edge Delivery Services (EDS) / AEM (XWalk)** site on `@adobe/aem-boilerplate` v1.3.0. Content is authored in AEM and delivered via the Franklin/Helix pipeline. The content source is an AEM Cloud author instance (`author-p75453-e1795963.adobeaemcloud.com`) configured in `fstab.yaml` with markup-based delivery.

The visual identity is defined in `design-reference.html` (the style guide).

## Architecture

EDS uses a document-based authoring model. Pages are authored in AEM, converted to semantic HTML, then decorated client-side by JavaScript. The page lifecycle follows three phases:

1. **Eager** (`loadEager`) - Decorates DOM, loads first section and fonts for LCP
2. **Lazy** (`loadLazy`) - Loads header, remaining sections, footer, lazy styles
3. **Delayed** (`loadDelayed`) - Loads `delayed.js` after 3 seconds for non-critical work

Blocks are auto-loaded: for a block with class `foo`, the system loads `blocks/foo/foo.js` and `blocks/foo/foo.css`.

## Design System — Tatry Topo

The full style guide is in `design-reference.html`. Below is the reference for implementation.

### Color Palette

**Granite Neutrals** (primary neutral scale, drawn from Tatra granite):
| Token | Hex | Usage |
|---|---|---|
| `--granite-900` | `#1A1D21` | Primary text, dark backgrounds, hero bg |
| `--granite-800` | `#252930` | Secondary dark backgrounds |
| `--granite-700` | `#353A42` | Dark UI elements |
| `--granite-600` | `#4A5060` | Secondary text (`--text-secondary`) |
| `--granite-500` | `#6B7280` | — |
| `--granite-400` | `#9CA3AF` | Muted text (`--text-muted`), hero subtitles |
| `--granite-300` | `#C4C9D4` | Strong borders (`--border-strong`) |
| `--granite-200` | `#E2E5EB` | Default borders (`--border-color`) |
| `--granite-100` | `#F0F2F5` | Light backgrounds, highlight sections |
| `--granite-50`  | `#F8F9FB` | Page background (`--bg-primary`) |

**Accent Colors**:
| Token | Hex | Usage |
|---|---|---|
| `--summit-orange` | `#E8652B` | Primary action color, links, CTAs, buttons |
| `--summit-orange-light` | `#F28A5A` | Hover states on dark backgrounds |
| `--summit-orange-dark` | `#C44E1A` | Hover states on light backgrounds |
| `--ridge-red` | `#D93B3B` | Error states, destructive actions |
| `--pine-green` | `#2D8C5A` | Success states |
| `--sky-blue` | `#3B82C4` | Informational |
| `--chalk-yellow` | `#E8B832` | Warnings |

**Grade Colors** (UIAA scale — only use for route difficulty):
| Token | Hex | Grade |
|---|---|---|
| `--grade-easy` | `#4AAF78` | I–III |
| `--grade-moderate` | `#3B82C4` | IV–V |
| `--grade-hard` | `#E8652B` | VI–VII |
| `--grade-extreme` | `#D93B3B` | VIII+ |
| `--grade-project` | `#9333EA` | Projekt |

### Typography

Four fonts loaded via Google Fonts (`fonts.css` lazy-loaded for performance):

| Variable | Font | Usage |
|---|---|---|
| `--font-display` | Bebas Neue | Hero titles, section headers, grade badges. ALL CAPS. Never below 20px. |
| `--font-heading` | Barlow Condensed | Headings h3-h6, nav, buttons, UI labels, tags. Weights 400–700. |
| `--font-body` | Barlow | Body text, descriptions, route notes. Weights 300–700. |
| `--font-mono` | JetBrains Mono | Coordinates, grades, hex values, technical data. Weights 400–500. |

**Heading hierarchy**:
- `h1` — Bebas Neue, `--heading-font-size-xxl` (55px), uppercase, letter-spacing 2px
- `h2` — Bebas Neue, `--heading-font-size-xl` (42px), uppercase, letter-spacing 1px
- `h3` — Barlow Condensed 700, `--heading-font-size-l` (32px)
- `h4` — Barlow Condensed 700, `--heading-font-size-m` (24px)
- `h5` — Barlow Condensed 600, `--heading-font-size-s` (18px)
- `h6` — Barlow Condensed 600, `--heading-font-size-xs` (14px), uppercase, letter-spacing 2px, muted color

### Spacing, Radius & Shadows

```
--radius-sm: 4px    --shadow-sm: 0 1px 3px rgba(26,29,33,0.08)
--radius-md: 8px    --shadow-md: 0 4px 12px rgba(26,29,33,0.10)
--radius-lg: 12px   --shadow-lg: 0 8px 30px rgba(26,29,33,0.12)
--radius-xl: 16px   --shadow-xl: 0 16px 50px rgba(26,29,33,0.16)
```

### Button Styles

- **Default/Primary**: Summit orange bg, white text, `--radius-md`, Barlow Condensed 600 uppercase
- **Secondary**: Transparent bg, `granite-300` border, dark text. On hover: border darkens.
- **Disabled**: `granite-200` bg, `granite-400` text

### Design Rules

1. `--summit-orange` is the primary action color — reserve for CTAs, active states, key data
2. Grade colors must ONLY be used for UIAA difficulty ratings, never decorative
3. Never use `Bebas Neue` for body text or below 20px
4. Never use `Barlow Condensed` for body/paragraph text
5. Max 2 accent colors per view
6. Use `--ridge-red` for errors, not `--summit-orange`
7. Dark sections use `--granite-900` background with `--text-inverse` text

## Key Files

### Root Configuration
- `fstab.yaml` - Content source mountpoint (AEM Cloud author instance, markup type)
- `paths.json` - AEM content path mappings (`/content/EDS/` -> `/`)
- `head.html` - Injected into every page `<head>` (CSP, viewport, Google Fonts preconnect, script/style includes)
- `helix-query.yaml` - Query index definition (generates `/query-index.json`)
- `helix-sitemap.yaml` - Sitemap generation config (source: query-index)
- `design-reference.html` - Complete visual style guide (colors, typography, components, usage rules)
- `404.html` - Custom 404 error page with SVG number display and back navigation
- `package.json` - Dev tooling only (linting, JSON merging, husky); no build step for site code

### Universal Editor / AEM Authoring (XWalk)
- `component-definition.json` - Defines available components (text, title, image, button, section, cards, columns, fragment, hero)
- `component-models.json` - Field definitions for each component's authoring dialog (properties, types, labels)
- `component-filters.json` - Controls which components can be placed inside which containers (e.g., `card` inside `cards`, `column` inside `columns`)
- `tools/sidekick/config.json` - AEM Sidekick configuration (project name, edit URL pattern)

### Per-Block Model Files (`models/` and `blocks/*/_*.json`)
JSON fragments that are merged via `npm run build:json` into the root `component-*.json` files:
- `models/_component-definition.json`, `models/_component-models.json`, `models/_component-filters.json` - Base definitions
- `models/_page.json`, `models/_section.json`, `models/_text.json`, `models/_title.json`, `models/_image.json`, `models/_button.json` - Core component models
- `blocks/hero/_hero.json`, `blocks/cards/_cards.json`, `blocks/columns/_columns.json`, `blocks/fragment/_fragment.json` - Block-specific model fragments

## Scripts (`scripts/`)

| File | Purpose |
|---|---|
| `aem.js` | Core EDS framework: RUM, DOM decoration (sections, blocks, buttons, icons), block loading, CSS/JS loading, image optimization. **Do not modify** - this is the AEM SDK. |
| `scripts.js` | Site-specific customization. Imports from `aem.js`. Contains `decorateMain()`, `loadEager/loadLazy/loadDelayed` lifecycle, `moveInstrumentation()` helper for Universal Editor. Entry point for page loading. |
| `delayed.js` | Placeholder for deferred logic (analytics, third-party scripts). Loaded 3s after page load. |
| `editor-support.js` | Universal Editor live preview. Handles `aue:content-*` events for in-place content updates without page reload. |
| `editor-support-rte.js` | Rich text editor support. Groups richtext-instrumented elements into editable wrappers for the Universal Editor. |
| `dompurify.min.js` | DOMPurify library for HTML sanitization (used by editor-support). |

## Blocks (`blocks/`)

Each block has its own directory with `blockname.js` (decoration logic) and `blockname.css` (styles). Blocks export a default `decorate(block)` function.

| Block | Description |
|---|---|
| `header/` | Navigation header. Dark granite-900 nav bar with Bebas Neue brand. Loads nav content as a fragment (from `/nav` or metadata override). Supports brand, sections (with dropdowns), and tools areas. Hamburger menu for mobile (<900px). |
| `footer/` | Dark footer (`granite-900` bg). Loads content as a fragment (from `/footer` or metadata override). |
| `hero/` | Full-bleed hero with `granite-900` background, overlaid Bebas Neue title, Barlow Condensed subtitle. Background images get a dark gradient overlay. Has empty `hero.js`. |
| `cards/` | Card grid with `--radius-xl` corners, `--shadow-sm` elevation, hover lift effect. Transforms block rows into `<ul>/<li>` structure. Uses `createOptimizedPicture`. |
| `columns/` | Multi-column layout. Auto-detects column count, adds `columns-N-cols` class. Images get `--radius-lg` corners. |
| `fragment/` | Content fragment inclusion. Fetches `.plain.html`, decorates and loads it as inline content. Exports `loadFragment()` used by header and footer. |

## Styles (`styles/`)

| File | Purpose |
|---|---|
| `styles.css` | Global styles loaded eagerly. Tatry Topo design tokens (granite palette, accents, grade colors, typography, radius, shadows), fallback font-face declarations, base typography, buttons, links, sections. |
| `fonts.css` | Google Fonts import for Bebas Neue, Barlow, Barlow Condensed, JetBrains Mono. Loaded after LCP on desktop or from session cache. |
| `lazy-styles.css` | Post-LCP global styles (currently empty placeholder). |

## Icons (`icons/`)

SVG icons referenced via `<span class="icon icon-{name}">`. Currently contains `search.svg`.

## Development

### Commands
- `npm run lint` - Run ESLint + Stylelint
- `npm run lint:fix` - Auto-fix lint issues
- `npm run lint:js` - ESLint only (JS + JSON)
- `npm run lint:css` - Stylelint only (blocks + styles CSS)
- `npm run build:json` - Merge per-block `_*.json` model fragments into root `component-*.json` files

### CI
GitHub Actions (`.github/workflows/main.yaml`) runs `npm ci && npm run lint` on every push.

### Linting
- ESLint: Airbnb base config with `eslint-plugin-xwalk` for EDS validation (`.eslintrc.js`)
- Stylelint: Standard config (`.stylelintrc.json`)
- Pre-commit hooks via Husky

## Conventions

### Adding a New Block
1. Create `blocks/blockname/blockname.js` with `export default function decorate(block) { ... }`
2. Create `blocks/blockname/blockname.css` for styles using Tatry Topo design tokens
3. Create `blocks/blockname/_blockname.json` with `definitions`, `models`, and `filters` arrays
4. Run `npm run build:json` to merge into root component JSON files
5. The block auto-loads when a `<div class="blockname">` is encountered in page content

### Button Styling
- Default button: link in a `<p>` tag (summit orange)
- Primary button: link wrapped in `<strong>` in a `<p>` tag (summit orange)
- Secondary button: link wrapped in `<em>` in a `<p>` tag (transparent + granite-300 border)

### Section Styles
Sections can have metadata-driven styles:
- `highlight` / `light` — `granite-100` background
- `dark` — `granite-900` background with inverse text

### Responsive Breakpoint
Single breakpoint at `900px` (mobile/tablet vs desktop). Used throughout styles and header JS. Container max-width is `1200px` with `24px` mobile / `48px` desktop padding.

### Content Fragments
Header and footer content come from AEM pages (`/nav` and `/footer` by default) loaded as fragments. Override via `nav` and `footer` metadata properties.
