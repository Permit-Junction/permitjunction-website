---
name: block-reference
description: Comprehensive reference for all blocks available in the Permit Junction website. Use this skill to understand block structure, variants, content models, and authoring guidelines for each block.
---

# Block Reference — Permit Junction Website

This skill is the definitive reference for all blocks available in this project. It documents each block's content model, variants, CSS classes, decoration behavior, and authoring guidelines based on source code analysis and live preview testing.

## Related Skills

- **da-content-authoring**: How to create and edit content in the DA editor (UI interactions)
- **content-modeling**: Design new block content models
- **building-blocks**: Implement new blocks or modify existing ones
- **block-collection-and-party**: Find reference implementations from the AEM Block Collection

## When to Use This Skill

Use this skill when:
- You need to know what blocks are available and how they work
- You're authoring content and need the correct block structure
- You want to understand a block's variants and configuration options
- You're debugging why a block isn't rendering correctly
- You need to know the content model contract between authors and code

Skip this skill when:
- You're writing block code (use building-blocks)
- You're designing a new content model from scratch (use content-modeling)
- You're performing DA editor interactions (use da-content-authoring)

## Block Inventory

| Block | Type | Auto-Blocked | DA Library | File |
|-------|------|-------------|------------|------|
| Hero | Standalone | No | Yes | `blocks/hero/` |
| Card | Collection | No | Yes | `blocks/card/` |
| Columns | Standalone | No | Yes | `blocks/columns/` |
| Table | Standalone | No | Yes | `blocks/table/` |
| Section Metadata | Configuration | No | Yes | `blocks/section-metadata/` |
| Metadata | Configuration | No | Yes | (page-level, no block folder) |
| YouTube | Auto-Block | Yes | No | `blocks/youtube/` |
| Advanced Tabs | Auto-Block | Yes | No | `blocks/advanced-tabs/` |
| Header | Fragment | No | No | `blocks/header/` |
| Footer | Fragment | No | No | `blocks/footer/` |
| Fragment | Utility | Yes | No | `blocks/fragment/` |
| Schedule | Utility | Yes | No | `blocks/schedule/` |

**DA Library blocks** (available in the Library panel > Blocks tab): Hero, Card, Section Metadata, Columns, Metadata, Table.

**Library block definitions** are stored in `docs/library/blocks/`. The buttons library page documents button styling patterns (not a block, but a decoration pattern).

---

## Hero Block

**Type**: Standalone
**File**: `blocks/hero/hero.js`, `blocks/hero/hero.css`
**Purpose**: Large banner/header area with optional background image or video and foreground text content.

### Content Model

```
| Hero |
|------|
| [Background image or video — optional] |
| [Heading, description, CTA links] |
```

- The **last row** is always the **foreground** (text content)
- The **second-to-last row** (if present) is the **background** (image or video)
- A single-row hero has no background image
- Background video: an mp4 link wrapping a picture element (the picture is the poster frame)

### Variants

Specified in the title row: `| Hero (variant1, variant2) |`

| Variant | Effect |
|---------|--------|
| `center` | Centers text horizontally and vertically |
| `small` | Reduced height (180px min-height) |
| `large` | Increased height (720px min-height) |
| `full` | Full viewport height (100vh) |
| `light` | Light color scheme (dark text on light background) |
| `dark` | Dark color scheme (light text on dark background) |
| `stack` | Stacked layout (no side-by-side at desktop) |
| `quiet-background` | Applies blur effect to background |

### Decoration Details

- Foreground (last row) becomes `.hero-foreground` with gradient overlay background
- Text containers within foreground get `.fg-text` class
- First heading gets class `hero-heading`
- Element before the heading gets class `hero-detail`
- First text column adds `hero-text-start` to the `.hero` element; subsequent columns add `hero-text-end`
- Background row (second-to-last, if present) becomes `.hero-background` with absolute positioning
- If background contains a video link wrapping a picture, it creates a `<video>` element with poster
- Default text color is `#fff` (white) — requires dark background or `light` variant for readability

### IMPORTANT: Hero Without Background Image

The hero block defaults to **white text** (`color: #fff`). Without a background image, the text is **invisible** on a white page background. The foreground gradient overlay (dark-to-transparent) is not sufficient for contrast without an image.

**Fix:** Always use the `light` variant when no background image is present:
- `Hero (center, light)` — centered with dark readable text
- `Hero (light)` — left-aligned with dark readable text

The `light` variant sets `color: var(--color-dark)` and changes the gradient to light-based, making text readable on any background.

### DA Library Templates

Two templates in `docs/library/blocks/hero`:
1. **hero** — With background image row + foreground text
2. **hero (center)** — Centered variant

### Example Combinations

- `Hero (light)` — Simple text hero, no background, dark readable text
- `Hero (center, light)` — Centered text, no background, dark readable text
- `Hero (center)` — Centered text WITH background image (white text on dark image)
- `Hero (large, dark)` — Full-width dark hero with large height
- `Hero (small, light)` — Compact light hero
- `Hero (full, center)` — Full-viewport centered hero with background image

---

## Card Block

**Type**: Collection
**File**: `blocks/card/card.js`, `blocks/card/card.css`
**Purpose**: Repeating card items, each with optional image, content, and CTA.

### Content Model

**IMPORTANT: Each card is a SEPARATE block table.** Do NOT put multiple cards as rows in a single card table. The grid layout for multiple cards comes from **section-metadata** (e.g., `grid | 3`), not from the card block itself.

**Single-column card (no image):**
```
| Card |
|------|
| [Bold title, description, CTA link] |
```

**Two-column card (with image):**
```
| Card |
|------|
| [Image] | [Heading, description, CTA link] |
```

Each card block table has exactly ONE data row containing the card's content. For a grid of 3 cards, you author 3 separate card block tables followed by section-metadata with `grid | 3`.

### Variants

| Variant | Effect |
|---------|--------|
| `quiet` | No shadow, no background, minimal styling |
| `center` | Centered text alignment |
| `hash-aware` | Appends current URL hash to CTA links |

### Decoration Details

- Each card row becomes a child div of the card block
- Pictures are extracted into `.card-picture-container` (60% aspect ratio)
- Text content goes into `.card-content-container`
- The last paragraph containing a link becomes `.card-cta-container`
- Default cards have `box-shadow` and hover scale effect
- Quiet cards strip the shadow and background

### Grid Layout

Cards are typically placed in a grid using **section-metadata**:

```
| Section Metadata |
|-----------------|
| style | center, container |
| grid | 3 |
| gap | l |
```

### DA Library Templates

Template in `docs/library/blocks/card` (currently empty — use the structure above).

---

## Columns Block

**Type**: Standalone (multi-column layout)
**File**: `blocks/columns/columns.js`, `blocks/columns/columns.css`
**Purpose**: Side-by-side column layout for content and images.

### Content Model

```
| Columns |
|---------|
| [Column 1 content] | [Column 2 content] |
| [Column 1 content] | [Column 2 content] |
```

- Each row is a separate row in the layout
- Each cell in a row is a column
- The number of columns per row sets the `--child-count` CSS variable

### Variants

| Variant | Effect |
|---------|--------|
| `gap-xs` | Extra small gap between columns |
| `gap-s` | Small gap |
| `gap-m` | Medium gap |
| `gap-l` | Large gap |
| `gap-xl` | Extra large gap |
| `gap-xxl` | Extra extra large gap |
| `image-cover` | Images fill their column with absolute positioning |
| `z-pattern` | Odd rows swap column order (content/image alternates) |
| `align-top` | Align column content to top instead of center |

### Decoration Details

- Each row gets `display: grid` with `grid-template-columns: repeat(var(--child-count), 1fr)`
- Responsive: collapses to single column below 900px
- `image-cover` variant: picture containers get `cover-image` class, other content gets `cover-content`
- `z-pattern`: CSS `direction: rtl` on odd rows swaps visual order

### DA Library Templates

Template in `docs/library/blocks/columns` (currently empty — use the structure above).

---

## Table Block

**Type**: Standalone
**File**: `blocks/table/table.js`, `blocks/table/table.css`
**Purpose**: Display structured tabular data with proper semantic HTML.

### Content Model

```
| Table |
|-------|
| Header 1 | Header 2 | Header 3 |
| Data 1   | Data 2   | Data 3   |
| Data 4   | Data 5   | Data 6   |
```

- First data row becomes `<thead>` with `<th>` elements
- Remaining rows become `<tbody>` with `<td>` elements
- Each data row gets class `table-content-row`

### Variants

None defined.

### Decoration Details

- Creates a semantic `<table>` element inside the block
- First row: `<thead>` > `<tr>` > `<th>` cells
- Remaining rows: `<tbody>` > `<tr>` > `<td>` cells
- The table block wraps a table authored as a nested table inside the block table in DA

### DA Library Templates

Template in `docs/library/blocks/table` — contains a nested table structure.

### Authoring Note

In DA, the Table block is a table-within-a-table: the outer table is the block container (with "Table" title row), and the inner content is the actual data table. The DA library template handles this nesting correctly.

---

## Section Metadata Block

**Type**: Configuration
**File**: `blocks/section-metadata/section-metadata.js`, `blocks/section-metadata/section-metadata.css`
**Purpose**: Configure the layout and appearance of the section it belongs to.

### Content Model

```
| Section Metadata |
|-----------------|
| style | center, container |
| grid | 3 |
| gap | l |
| spacing | xl |
| background | color-token-purple-200 |
```

Always placed as the **last block in a section** (before the section break).

### Configuration Keys

| Key | Values | Effect |
|-----|--------|--------|
| `style` | Comma-separated CSS classes (e.g., `center`, `container`) | Applied as classes to the section |
| `grid` | `2`, `3`, `4`, `5`, `6` | Number of grid columns for child blocks |
| `gap` | `xs`, `s`, `m`, `l`, `xl`, `xxl` | Gap between grid items |
| `spacing` | `xs`, `s`, `m`, `l`, `xl`, `xxl` | Section padding (top and bottom) |
| `container` | `2`, `4`, `6` | Container width constraint (in grid columns) |
| `layout` | `bento` | Special bento grid layout with named areas |
| `background` | Color token (e.g., `color-token-purple-200`) | Section background color |
| `background-color` | Color token | Alternative to `background` for color |
| `background-image` | Image | Background image for the section |

### Decoration Details

- Parses key-value pairs from the 2-column table
- `style` values are split by comma and added as CSS classes to the parent section
- `grid` sets `grid-template-columns: repeat(N, 1fr)` on the section
- `background` creates a `.section-background` div with absolute positioning and the specified color
- `background-image` creates a `.section-background` with a picture element
- Exports `getColorScheme()` and `setColorScheme()` for light/dark detection
- The section-metadata block itself is hidden after decoration (`display: none`)

### DA Library Templates

Template in `docs/library/blocks/section-metadata` — includes all common keys.

### Common Patterns

**Card grid section:**
```
| Section Metadata |
|-----------------|
| style | center, container |
| grid | 3 |
| gap | l |
| spacing | xxl |
```

**Colored background section:**
```
| Section Metadata |
|-----------------|
| style | center |
| spacing | xxl |
| background | color-token-purple-200 |
```

---

## Metadata Block

**Type**: Configuration (page-level)
**Purpose**: Set page-level metadata (title, description, social image, template).

### Content Model

```
| Metadata |
|----------|
| title | Page Title Here |
| description | Meta description for SEO |
| image | [image] |
| template | template-name |
```

Placed at the **very end of the page**, outside any section.

### Configuration Keys

| Key | Values | Effect |
|-----|--------|--------|
| `title` | Text | Page `<title>` and og:title |
| `description` | Text | Meta description and og:description |
| `image` | Image | og:image for social sharing |
| `template` | Template name | Page template selection |

### DA Library Templates

Template in `docs/library/blocks/metadata` (currently empty — use the structure above).

---

## YouTube Block (Auto-Blocked)

**Type**: Auto-Block
**File**: `blocks/youtube/youtube.js`, `blocks/youtube/youtube.css`
**Purpose**: Embed YouTube videos with privacy-enhanced mode.

### Content Model

No block table needed. Simply paste a YouTube URL as a link in default content:

```
https://www.youtube.com/watch?v=VIDEO_ID
```

The link is auto-detected by the `linkBlocks` configuration in `scripts.js` and converted to a YouTube embed block.

### Auto-Blocking Trigger

From `scripts.js`:
```javascript
{ youtube: 'https://www.youtube' }
```

Any link starting with `https://www.youtube` is auto-blocked.

### Decoration Details

- Extracts video ID from `?v=` parameter or URL path
- Creates privacy-enhanced embed: `https://www.youtube-nocookie.com/embed/{id}`
- Preserves URL parameters (except `v`)
- Adds `rel=0` parameter to disable related videos
- Lazy-loads via intersection observer (`scripts/utils/observer.js`)
- 16:9 aspect ratio via `padding-bottom: 56.25%`

### Authoring Note

To add a YouTube video in DA:
1. Type or paste the full YouTube URL
2. Select the URL text
3. Click "Edit link" and set the URL as the link target
4. The link will be auto-blocked on preview/publish

To prevent auto-blocking, append `#_dnb` to the URL.

### Known Rendering Behavior

The YouTube embed **lazy-loads** via IntersectionObserver. The `.video` container has no min-height before the iframe loads, so it may appear as an invisible empty space until the user scrolls to it. This is expected behavior — the embed renders once it enters the viewport.

---

## Advanced Tabs Block (Auto-Blocked)

**Type**: Auto-Block
**File**: `blocks/advanced-tabs/advanced-tabs.js`, `blocks/advanced-tabs/advanced-tabs.css`
**Purpose**: Tabbed content interface created from sections.

### Content Model

This block is auto-blocked from a specific content structure. Authors create:

1. A section containing a `<ul>` list with tab names
2. Followed by sections (one per tab), each starting with an `<h2>` heading matching a tab name

```
- Tab One
- Tab Two
- Tab Three
---
## Tab One
[Content for tab one...]
---
## Tab Two
[Content for tab two...]
---
## Tab Three
[Content for tab three...]
```

### Decoration Details

- The `<ul>` list becomes the tab button bar
- Each list item becomes a `<button>` with `role="tab"`
- Sibling sections become tab panels with `role="tabpanel"`
- Active tab gets `is-active` class; visible panel gets `is-visible`
- Click handlers toggle between tabs
- First tab is active by default
- ARIA attributes for accessibility (`aria-selected`, `aria-controls`, `aria-labelledby`)

### Authoring Note

The tab names in the `<ul>` list must correspond to the `<h2>` headings in the following sections. The number of list items should match the number of subsequent sections.

---

## Header Block (Fragment)

**Type**: Fragment-based
**File**: `blocks/header/header.js`
**Purpose**: Site header with navigation, brand, and actions.

### Content Source

Loaded automatically from `/fragments/nav/header`. Not authored inline on pages.

### Structure

The header fragment has 3 sections:
1. **Brand**: Logo and site name
2. **Navigation**: Mega menu with nested lists
3. **Actions**: Color scheme toggle, language selector, mobile menu toggle

### Decoration Details

- Brand section: wraps in `.header-brand`
- Nav section: creates `.header-nav` with expandable mega menu
- Actions section: `.header-actions` with scheme toggle button, language button
- Mobile: hamburger toggle (`.header-toggle`) shows/hides nav
- Mega menu items with child lists become expandable dropdowns

---

## Footer Block (Fragment)

**Type**: Fragment-based
**File**: `blocks/footer/footer.js`
**Purpose**: Site footer with links and legal text.

### Content Source

Loaded automatically from `/fragments/nav/footer`. Not authored inline on pages.

### Structure

- Last section = copyright
- Second-to-last section = legal links
- Additional sections for footer content (navigation, social links, etc.)

---

## Fragment Block (Utility)

**Type**: Utility / Auto-Block
**File**: `blocks/fragment/fragment.js`
**Purpose**: Load and inline content from another page.

### Auto-Blocking Trigger

From `scripts.js`:
```javascript
{ fragment: '/fragments/' }
```

Any link with a path containing `/fragments/` is auto-blocked as a fragment.

### Usage

In default content, add a link to a fragment path:
```
/fragments/some-content
```

The fragment block fetches the HTML from that path, decorates it, and replaces the link with the fragment content.

---

## Schedule Block (Utility)

**Type**: Utility / Auto-Block
**File**: `blocks/schedule/schedule.js`
**Purpose**: Time-based content scheduling.

### Auto-Blocking Trigger

From `scripts.js`:
```javascript
{ schedule: '/schedules/' }
```

### Usage

Links to `/schedules/` paths are auto-blocked. The block fetches JSON schedule data, finds the matching event by date range, and loads the corresponding fragment.

For non-production testing, set `localStorage.setItem('aem-schedule', '2024-06-15T10:00:00')` to simulate a specific date/time.

---

## Button Styles (Not a Block)

Buttons are not a separate block — they are a **decoration pattern** applied to links based on surrounding markup. The `ak.js` framework auto-converts links to styled buttons.

### Patterns

| Author Markup | CSS Class | Style |
|--------------|-----------|-------|
| `***text***` (bold+italic) | `.btn-accent` | Accent/CTA button |
| `**text**` (bold) | `.btn-primary` | Primary button |
| `*text*` (italic) | `.btn-secondary` | Secondary button |
| `~~text~~` (strikethrough) | `.btn-negative` | Negative/danger button |
| Any above + `<u>` (underline) | adds `.btn-outline` | Outline variant |

### Button Groups

Multiple styled links in the same paragraph create a button group (`.btn-group`):

```
***Accent*** **Primary** *Secondary*
```

(Each word is a separate link with the respective formatting.)

### Hash Modifiers

| Hash | Effect |
|------|--------|
| `#_blank` | Opens link in new tab |
| `#_dnt` | Do not localize this link |
| `#_dnb` | Do not auto-block this link |

### DA Library Reference

The `docs/library/blocks/buttons` page contains comprehensive examples of all button patterns, including accent, primary, secondary, negative, outline variants, button groups, and inline links.

---

## Auto-Blocking Configuration

Auto-blocking is configured in `scripts/scripts.js` via the `linkBlocks` array:

```javascript
const linkBlocks = [
  { fragment: '/fragments/' },
  { schedule: '/schedules/' },
  { youtube: 'https://www.youtube' },
];
```

When a link in default content matches a pattern, it is automatically converted to the corresponding block. To prevent auto-blocking on a specific link, append `#_dnb` to the URL.

## Self-Managed Style Blocks

Blocks listed in the `components` array in `scripts.js` manage their own CSS loading:

```javascript
const components = ['fragment', 'schedule'];
```

These blocks skip automatic CSS loading by the framework.

---

## DA Library Structure

The DA Library panel (accessible via the "Library" toolbar button) provides pre-built block templates for authors. Library definitions are stored in `docs/library/blocks/`:

```
docs/library/blocks/
  buttons      # Button pattern reference (not a block)
  card         # Card block template
  columns      # Columns block template
  hero         # Hero block templates (default + center variant)
  metadata     # Page metadata template
  section-metadata  # Section metadata template
  table        # Table block template (nested table structure)
```

Each library page contains one or more example block tables that authors can insert by clicking the block name in the Library panel. The block name must match the block title row exactly.

### Library Block Templates Available

| Library Entry | Variants Included |
|--------------|-------------------|
| Hero | `hero`, `hero (center)` |
| Card | (empty — needs template) |
| Columns | (empty — needs template) |
| Section Metadata | All common keys |
| Metadata | (empty — needs template) |
| Table | Nested table example |
| Buttons | All button patterns |

---

## Key Authoring Rules

1. **Block title rows must be a single merged cell** containing only the block name (and optional variants in parentheses). A title row with multiple cells will not be recognized as a block.

2. **Each card is a separate block table.** Do NOT put multiple cards as rows in one table. For a grid of cards, author individual card tables and use section-metadata with `grid | N` to arrange them.

3. **Section metadata goes last** in a section, immediately before the section break (horizontal rule).

4. **Page metadata goes at the very end** of the page, outside any section.

5. **Auto-blocked links** are converted automatically — no block table needed. Just paste the URL as a link.

6. **Variants are specified in the title row** in parentheses: `| Hero (center, large) |`

7. **Button styling comes from text formatting**, not from a block. Bold = primary, bold+italic = accent, italic = secondary.

8. **Grid layouts require section-metadata** — cards and other collection blocks don't grid themselves. Use `grid` key in section-metadata.

9. **Maximum 4 cells per row** in any block content model.

10. **Fragment blocks load external content** — header and footer are fragments loaded from `/fragments/nav/`. Custom fragments can be loaded from any `/fragments/` path.

11. **Hero blocks need `light` variant without a background image.** The default white text is invisible on a white page. Use `Hero (light)` or `Hero (center, light)` for text-only heroes.

12. **When pasting block HTML programmatically**, use `colspan="N"` on the title row `<td>` for any block with multi-column data rows (section-metadata, columns, metadata). ProseMirror normalizes column counts and will split title rows without colspan.
