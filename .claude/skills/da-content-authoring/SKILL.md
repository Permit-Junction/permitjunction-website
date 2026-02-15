---
name: da-content-authoring
description: Author and edit content in DA (Document Authoring) at da.live for AEM Edge Delivery Services. Use this skill for creating pages, editing text, inserting blocks, managing sections, adding media, previewing content, and all content authoring tasks performed in the DA editor.
---

# DA Content Authoring for AEM Edge Delivery Services

This skill guides you through authoring and editing content in DA (Document Authoring) at `da.live`. DA is the web-based content editor for AEM Edge Delivery Services sites.

## Related Skills

- **content-driven-development**: Use CDD for development workflows; this skill handles the content authoring side
- **content-modeling**: Understand block content models before authoring block content
- **building-blocks**: Block implementation; use this skill's knowledge to author test content for blocks

## When to Use This Skill

Use this skill when:
- Creating or editing page content in DA
- Adding, modifying, or removing blocks in authored content
- Managing sections and section metadata
- Inserting or editing media (images, videos)
- Creating new pages or managing site content structure
- Previewing authored content
- Working with versions/history

Skip this skill when:
- Writing block code (use building-blocks instead)
- Doing development workflow (use content-driven-development instead)
- Only reading/inspecting content (just navigate to DA directly)

## DA Overview

**URL**: `https://da.live/edit#/{org}/{site}/{path}`
**For this project**: `https://da.live/edit#/permit-junction/permitjunction-website/{path}`
**File browser**: `https://da.live/#/permit-junction/permitjunction-website`
**Documentation**: `https://docs.da.live`

DA is a web-based WYSIWYG editor that stores content as HTML, which AEM Edge Delivery Services converts to the semantic HTML served to visitors. Content is auto-saved as you type. Changes are committed to the GitHub repository automatically.

### Key Concepts

- **Pages** are HTML documents edited in a rich text editor
- **Blocks** appear as tables in the editor with a title row containing the block name
- **Sections** are separated by horizontal rules (section breaks)
- **Default content** is text, headings, images, and links outside of blocks
- **Section metadata** is a special block at the end of a section configuring layout

## DA Editor Interface

### Top Bar

| Element | Description |
|---------|-------------|
| **Breadcrumb** (e.g., `permitjunction-website > index`) | Navigate up the content hierarchy. Cmd/Ctrl+Click opens in new tab |
| **Send button** | Opens AI chat/assistant panel for content generation |
| **Preview button** | Triggers content preview on the `.aem.page` domain |
| **Versions button** | Opens version history panel |

### Toolbar (Contextual)

The toolbar adapts based on what element the cursor is in. Buttons toggle between states depending on context.

#### Always Visible

| Button | Label | Description |
|--------|-------|-------------|
| **Edit text** | (paintbrush icon) | Opens text formatting and heading level panel |
| **Edit link** | `Edit link` | Add or edit a hyperlink (select text first) |
| **List** | `List` | Insert or modify bullet/ordered lists |
| **Library** | `Library` | Open the content library panel (blocks, templates, icons, AI) |
| **Block / Edit block** | `Block` or `Edit block` | Insert a new block (when in default content) OR edit block table structure (when cursor is inside a block) |
| **Section** | `Section` | Insert a section break (horizontal rule) |
| **Undo** | `Undo` | Undo last change |
| **Redo** | `Redo` | Redo last undone change |

### Edit Text Panel

Opened by clicking the **Edit text** button. Contains two rows:

**Inline formatting:**
| Button | Action |
|--------|--------|
| **B** | Toggle bold |
| **I** | Toggle italic |
| **U** | Toggle underline |
| **S** | Toggle strikethrough |
| **SUP** | Toggle superscript |
| **SUB** | Toggle subscript |
| **(code icon)** | Toggle inline code |

**Block-level formatting:**
| Button | Action |
|--------|--------|
| **P** | Change to paragraph |
| **H1** | Change to Heading 1 |
| **H2** | Change to Heading 2 |
| **H3** | Change to Heading 3 |
| **H4** | Change to Heading 4 |
| **H5** | Change to Heading 5 |
| **H6** | Change to Heading 6 |
| **Code** | Change to code block |

### List Menu

Opened by clicking the **List** button:

| Button | Action |
|--------|--------|
| **Bullet List** | Create/toggle unordered list |
| **Ordered List** | Create/toggle ordered list |
| **Indent List** | Increase list indentation |
| **Outdent List** | Decrease list indentation |

### Edit Block Menu (Table Operations)

When cursor is inside a block (table), the **Block** button becomes **Edit block**. Clicking it reveals:

| Button | Action |
|--------|--------|
| **Insert column before** | Add column to the left |
| **Insert column after** | Add column to the right |
| **Delete column** | Remove current column |
| **Insert row before** | Add row above |
| **Insert row after** | Add row below |
| **Delete row** | Remove current row |
| **Delete table** | Delete the entire block/table |

### Library Panel

Opened by clicking **Library**. Contains four tabs:

1. **Blocks** - Pre-configured block templates from the project's `docs/library/blocks/` folder. Each block has copy and preview actions. Click a block name to insert it at cursor position.
2. **Templates** - Full page templates. Click to create a new page from template.
3. **Icons** - SVG icons from the project's `icons/` folder. Click to insert an icon reference.
4. **Generate Variations** - AI-powered content variation generation (requires Adobe IMS auth).

Library blocks for this project: Hero, Card, Section Metadata, Columns, Metadata, Table.

## How to Perform Common Tasks via Playwright

### Navigate to a Page

```
browser_navigate: https://da.live/edit#/permit-junction/permitjunction-website/{path}
browser_wait_for: time=3  (wait for editor to load)
```

### Edit Text in the Editor

**CRITICAL**: DA uses a rich text editor (ProseMirror-based). Direct DOM manipulation will NOT work. You must use keyboard-based interactions.

**To replace text within a specific element (e.g., a heading):**

1. Locate the element in the snapshot (e.g., `heading "Old Text" [level=1] [ref=eXXX]`)
2. Get the element's bounding box and double-click near the start to select first word
3. Extend selection with `Shift+Control+ArrowRight` to select additional words
4. Type replacement text

```javascript
// Example: Replace heading text
async (page) => {
  const h1 = page.locator('h1:has-text("Old Text")').first();
  const box = await h1.boundingBox();
  // Double-click to select first word
  await page.mouse.dblclick(box.x + 20, box.y + box.height / 2);
  await page.waitForTimeout(300);
  // Extend selection to cover all words
  await page.keyboard.press('Shift+Control+ArrowRight'); // repeat as needed
  await page.waitForTimeout(200);
  // Type replacement
  await page.keyboard.type('New Text');
}
```

**WARNING**: Do NOT use triple-click or Home/Shift+End inside block table cells - these select the entire cell content, destroying sibling elements. Always use double-click + Shift+Ctrl+Arrow to select precisely within a single element.

**To add new text:**
1. Click at the desired location in the editor
2. Use `browser_type` or `page.keyboard.type()` to enter text
3. Press Enter to create new paragraphs

**To change heading level:**
1. Click in the heading text
2. Click "Edit text" button
3. Click the desired heading level (H1-H6) or P for paragraph

### Insert a Block

**Via toolbar:**
1. Place cursor where you want the block (in default content, NOT inside another block)
2. Click the "Block" button (labeled `Insert block` in the toolbar, or accessible via `ref`)
3. Type the block name in the dialog that appears

**Via Library:**
1. Place cursor at insertion point
2. Click "Library" button
3. Click "Blocks" tab
4. Click the desired block name to insert its template

### Insert a Section Break

1. Place cursor at the desired location
2. Click the "Section" button in the toolbar
3. A horizontal rule (section break) is inserted

### Add a Link

1. Select the text you want to make into a link
2. Click "Edit link" button in the toolbar
3. Enter the URL in the dialog
4. For button styling, wrap the link text in formatting:
   - `**text**` (bold) = primary button
   - `***text***` (bold+italic) = accent button
   - `*text*` (italic) = secondary button

### Add an Image

**Drag and drop**: Drag an image file directly onto the editor canvas.

**From media folder**: Images are stored in a folder matching the document name. You can also use the top-level `media/` folder.

**Supported formats**: JPG, PNG, GIF, SVG, PDF, MP4

### Preview Content

There are two ways to preview content:

**Method 1: Full Page Preview (Recommended)**
1. Click the **blue paper airplane icon** (Send button) in the top-right toolbar area
2. This expands to reveal **Preview** and **Publish** buttons
3. Click **Preview** to open a full preview in a new browser tab at `https://main--permitjunction-website--permit-junction.aem.page/{path}`
4. This triggers the AEM Edge Delivery pipeline to build and serve the page with full block decoration, header, and footer

**Method 2: Inline Preview Panel**
1. Click the **Preview** button in the bottom-right toolbar area
2. The preview renders in an iframe panel on the right side of the editor
3. This is useful for quick checks but may show errors for header/footer fragments

**IMPORTANT**: You may preview content but **NEVER publish** it. Publishing is reserved for human operators. The blue paper airplane menu also shows a Publish option — do NOT click it.

### Work with Versions

1. Click the **Versions** button in the bottom-right area
2. The history panel shows edits in reverse chronological order:
   - **Gray entries** = individual edits (expandable for editor/timestamp)
   - **Green entries** = saved versions
3. To create a version: click the `+` icon next to "Now" at the top, enter a label
4. To restore: select a version, click the green arrow, then "Restore"

## File Browser

**URL**: `https://da.live/#/permit-junction/permitjunction-website`

### Features

- **Browse tab**: Navigate folders and files in a grid view
- **Search tab**: Search content across the site
  - Search tips: `class="block-name"` to find blocks, `href="url"` to find links
- **New button**: Create a new page or folder
- **Breadcrumbs**: Navigate hierarchy; includes "Config" link for site settings
- **Checkboxes**: Select items to reveal the action bar (copy, move, delete, etc.)
- **Drag and drop**: Drop content files into the file list area

### Site Structure in DA

```
permit-junction/permitjunction-website/
  docs/            # Library definitions (blocks, templates)
  fragments/       # Reusable content fragments (header, footer, etc.)
  index            # Homepage
  media/           # Shared media folder
  schedules/       # Content scheduling data
```

## Content Structure Rules

### Pages
- A page is divided into **sections** separated by section breaks (horizontal rules)
- Each section can contain **default content** (text, headings, images, links) and **blocks** (tables)
- The last block in a section can be **section-metadata** to configure layout

### Blocks in the Editor
- Blocks appear as **tables** with a colored title row
- The title row contains the block name and optional variants in parentheses: `hero (center)`, `card (quiet, center)`
- Block rows contain the content for that block
- Multi-column blocks have multiple cells per row

### Section Metadata
A special block placed at the end of a section to configure section layout:

| Key | Values | Effect |
|-----|--------|--------|
| `style` | `center`, `container`, etc. | CSS classes applied to section |
| `grid` | `2`, `3`, `4`, etc. | Number of grid columns |
| `gap` | `xs`, `s`, `m`, `l`, `xl`, `xxl` | Gap between grid items |
| `spacing` | `xs`, `s`, `m`, `l`, `xl`, `xxl` | Section padding |
| `container` | `4`, `6`, `8`, etc. | Container width (in grid columns) |
| `background` | color token (e.g., `color-token-purple-200`) | Section background color |

### Metadata Block
A page-level metadata block (typically at the very end of the page, outside any section):

| Key | Values | Effect |
|-----|--------|--------|
| `title` | text | Page title for SEO |
| `description` | text | Meta description |
| `image` | image | Social sharing image |
| `template` | template name | Page template |

## Injecting Rich HTML Content via Paste

DA uses a ProseMirror-based editor inside nested shadow DOM. To programmatically inject rich HTML content (block tables, formatted text, etc.), use the ClipboardEvent paste technique.

### Shadow DOM Path

```
document (light DOM)
  └─ DA-CONTENT (custom element)
       └─ shadowRoot
            └─ .editor-wrapper
                 └─ DA-EDITOR (custom element)
                      └─ shadowRoot
                           └─ .ProseMirror (contenteditable div)
```

### Recommended Strategy: Full-Page Single Paste

**ALWAYS paste an entire page's content in a single ClipboardEvent.** This is the proven, reliable approach. Sequential pastes (pasting section by section) WILL FAIL because content gets nested inside the last table cell rather than appending at the document level.

**Prerequisites:**
1. Start with a **fresh empty page** (just an empty paragraph in the editor)
2. Click on the empty paragraph to focus the editor and position the cursor
3. Build the complete HTML for the entire page, then paste it all at once

**If the page has existing content that needs replacing:** Rename the existing page in the file browser (to preserve it as a backup), then create a new page with the original name (see "Replacing an Existing Page" below). Do NOT try select-all + delete — ProseMirror table structures often survive deletion, leaving corrupted state.

### Paste HTML via Playwright

**IMPORTANT:** Use `browser_evaluate` (not `browser_run_code`) because the paste requires page context where `DataTransfer` and `ClipboardEvent` are available.

```javascript
// Use browser_evaluate — runs in page context where DataTransfer exists
() => {
  const daContent = document.querySelector('da-content');
  const contentShadow = daContent.shadowRoot;
  const daEditor = contentShadow.querySelector('da-editor');
  const editorShadow = daEditor.shadowRoot;
  const pmEl = editorShadow.querySelector('.ProseMirror');

  const html = `<h1>My Heading</h1><p>My paragraph</p>`;
  const clipboardData = new DataTransfer();
  clipboardData.setData('text/html', html);
  const pasteEvent = new ClipboardEvent('paste', {
    bubbles: true,
    cancelable: true,
    clipboardData: clipboardData,
  });
  pmEl.dispatchEvent(pasteEvent);
  return 'Paste dispatched';
}
```

### Critical: colspan on Block Title Rows

**ProseMirror normalizes table column counts.** If a block's data rows have 2 columns, ProseMirror will pad the title row to 2 cells (prepending an empty cell), breaking block recognition.

**The fix:** Use `colspan="N"` on the title row's `<td>`, where N matches the number of columns in data rows.

**Blocks that NEED colspan** (have multi-column data rows):
- `section-metadata` → `<tr><td colspan="2">section-metadata</td></tr>`
- `columns` → `<tr><td colspan="2">columns (z-pattern)</td></tr>`
- `metadata` → `<tr><td colspan="2">metadata</td></tr>`

**Blocks that DON'T need colspan** (single-column data rows):
- `hero` → `<tr><td>hero (center, light)</td></tr>`
- `card` → `<tr><td>card</td></tr>` or `<tr><td>card (quiet, center)</td></tr>`

### Full-Page Paste Example

Here is a proven HTML structure for a complete page with multiple sections:

```html
<table><tr><td>hero (center, light)</td></tr>
<tr><td><h1>Page Title</h1><p>Description text.</p>
<p><em><strong><a href="https://example.com">CTA Button</a></strong></em></p></td></tr></table>
<hr>
<h2>Section Heading</h2>
<p>Intro text with <em><strong><a href="url">Accent Button</a></strong></em></p>
<table><tr><td>card</td></tr>
<tr><td><p><strong>Card Title</strong></p><p>Description.</p>
<p><a href="url"><strong>Primary CTA</strong></a></p></td></tr></table>
<table><tr><td>card</td></tr>
<tr><td><p><strong>Card Title 2</strong></p><p>Description.</p>
<p><a href="url"><strong>Primary CTA</strong></a></p></td></tr></table>
<table><tr><td colspan="2">section-metadata</td></tr>
<tr><td>style</td><td>center, container</td></tr>
<tr><td>grid</td><td>2</td></tr>
<tr><td>spacing</td><td>xxl</td></tr></table>
<hr>
<table><tr><td colspan="2">metadata</td></tr>
<tr><td>title</td><td>Page Title</td></tr>
<tr><td>description</td><td>Page description for SEO.</td></tr></table>
```

**Key patterns in this example:**
- Each card is a **separate table** (NOT multiple rows in one table). The grid layout comes from section-metadata's `grid` key.
- Section breaks (`<hr>`) separate sections.
- Section-metadata goes at the **end** of a section, before the `<hr>`.
- Metadata block goes in the **last section** of the page.
- `colspan="2"` on section-metadata, metadata title rows.
- Button links use semantic formatting: `<em><strong><a>` = accent, `<a><strong>` = primary, `<a><em>` = secondary.

### Replacing an Existing Page (Preferred)

When replacing an existing page's content entirely, **rename the original first** to preserve it as a backup, then create a fresh page with the original name:

1. Navigate to the file browser: `https://da.live/#/permit-junction/permitjunction-website`
2. Select the page's checkbox (use `force: true` via `browser_run_code` — `<label>` elements intercept pointer events on checkboxes)
3. Click the **Rename** button in the action bar
4. Clear the textbox (`Meta+a` then type), enter the new name (e.g., `index-original`), click **Confirm**
5. Click **New** → **Document** → type the original page name (e.g., `index`) → click **Create document**
6. Wait 3 seconds for the fresh editor to load
7. Click on the empty paragraph to focus the editor
8. Paste the complete HTML content via `browser_evaluate`

**Why rename instead of delete:** Renaming preserves the original content as a backup. The owner can reference it later or restore it if needed. Deleting is permanent and irreversible.

### Resetting a Corrupted Page

When a page has corrupted ProseMirror state (orphaned table structures, broken blocks, etc.) and you don't need to preserve the original, you can delete and recreate:

1. Navigate to the file browser: `https://da.live/#/permit-junction/permitjunction-website`
2. Select the page's checkbox (use `force: true` via `browser_run_code` — `<label>` elements intercept pointer events)
3. Click the **Delete** button in the action bar
4. Confirm deletion in the dialog (use `force: true` click on the confirm button)
5. Click **New** → **Document** → enter the page name → click **Create document**
6. Wait 3 seconds for the fresh editor to load
7. Click on the empty paragraph to focus the editor
8. Paste the complete HTML content

**Why this works:** A fresh page has a clean ProseMirror state with just an empty paragraph. Pasting into this clean state avoids column normalization issues caused by residual table DOM.

### File Browser Action Workflows

**Selecting items:** Checkboxes in the file browser are intercepted by `<label>` elements. Use `browser_run_code` with `force: true`:
```javascript
async (page) => {
  const checkbox = page.locator('input#item-selected-N'); // N = item index (0-based)
  await checkbox.click({ force: true });
  return 'Selected';
}
```

**Renaming a page:**
1. Select the page checkbox (force click)
2. Click **Rename** in the action bar (normal `browser_click` works)
3. Click the rename textbox, `Meta+a` to select all, then `browser_type` the new name
4. Click **Confirm**

**Creating a new document:**
1. Click **New** button
2. Click **Document** from the dropdown
3. Type the name in the textbox
4. Click **Create document**
5. DA auto-navigates to the new page's editor — wait 3 seconds for it to load

### What Does NOT Work

- **Sequential pastes**: Content from second paste onwards nests INSIDE the last table cell of the first paste. Always paste everything in one shot.
- **Select-all + Delete for cleanup**: ProseMirror tables often survive Cmd+A + Backspace, leaving orphaned table structures. Delete the page from file browser instead.
- **Direct DOM manipulation**: ProseMirror manages its own state. Direct DOM changes are ignored or cause corruption.
- **ProseMirror view access**: The PM EditorView is not exposed on any accessible property. Cannot programmatically replace the document via PM API.
- **CORS to admin.da.live**: `fetch()` from da.live to admin.da.live is blocked by CORS. Cannot write HTML directly via the admin API.
- **browser_run_code for paste**: Runs in Playwright context, not page context. `DataTransfer` and `ClipboardEvent` are not available. Use `browser_evaluate` instead.
- **Bounding box clicks on shadow DOM elements**: `boundingBox()` from `browser_run_code` on shadow DOM elements returns incorrect coordinates. Use MCP ref-based clicks instead (e.g., `browser_click` with `ref=eXXX`).

## Editing Text in Block Title Cells

To change a block's name or variants (e.g., adding `light` to a hero):

1. Click on the title text using MCP ref-based click: `browser_click` on the paragraph ref inside the title cell
2. Press `Home` to move cursor to start of line
3. Press `Shift+End` to select the entire line
4. Use `browser_type` with the ref to type the replacement text

**This is safe** for title cells that contain only a single paragraph with the block name. The Home/Shift+End warning only applies to cells with multiple elements (headings, paragraphs, links) where it would destroy siblings.

```
// Example: Change "hero (center)" to "hero (center, light)"
browser_click: ref=e124  (the paragraph containing "hero (center)")
browser_press_key: Home
browser_press_key: Shift+End
browser_type: ref=e124, text="hero (center, light)"
```

## Block Variant Tips

### Hero Block Without Background Image

The hero block defaults to `color: #fff` (white text). Without a background image, the text is invisible on a light background.

**Fix:** Add the `light` variant → `hero (center, light)`. This sets:
- `color: var(--color-dark)` — dark, readable text
- Light gradient overlay instead of dark

**Available hero variants:**
- `center` — centers text horizontally
- `light` — dark text for use without dark backgrounds
- `dark` — explicitly light text (default behavior)
- `small` / `large` / `full` — controls min-height
- `stack` — stacks background and foreground vertically
- `quiet-background` — blurred background effect

### Card Variants

- `quiet` — removes card border/shadow for minimal look
- `center` — centers card text
- Combine: `card (quiet, center)` for centered minimal cards

## Tips for Automated Authoring

1. **Always wait for the editor to load** (3 seconds minimum after navigation)
2. **Use snapshots** (`browser_snapshot`) to find current element refs before interacting
3. **Auto-save**: DA auto-saves changes; no explicit save action needed
4. **Contextual toolbar**: The toolbar changes based on cursor position. Click into the right area first.
5. **Rich text caution**: Never try to manipulate the DOM directly. Always use keyboard-based editing or the paste technique above.
6. **Block insertion**: Place cursor in default content (outside any table) before inserting blocks.
7. **Text selection precision**: Double-click selects a word. Shift+Ctrl+Arrow extends word-by-word. Avoid triple-click or Home/End inside multi-element table cells.
8. **Preview vs Publish**: Always use Preview. NEVER use Publish.
9. **Paper airplane button**: The blue paper airplane icon at top-right opens the Preview/Publish menu. Use Preview for full page preview in a new tab.
10. **Block title row format**: When creating blocks (manually or via paste), the title row must be a single merged cell containing only the block name and optional variants.
11. **Full-page paste**: When creating or recreating page content, always paste ALL content in a single ClipboardEvent on a fresh empty page.
12. **colspan is mandatory**: Any block with multi-column data rows MUST use `colspan` on the title row `<td>` to prevent ProseMirror from splitting it into multiple cells.
13. **Cards are individual tables**: Each card is its own `<table>` block. Grid layout of multiple cards comes from section-metadata's `grid` key, not from putting multiple rows in one card table.
14. **File browser force clicks**: Checkboxes and dialog buttons in the DA file browser need `force: true` because `<label>` and `<span>` elements intercept pointer events.
15. **Screenshots go in `/tmp`**: When taking screenshots during authoring sessions (for visual verification), always save them to `/tmp/` (e.g., `filename: /tmp/test-page-preview.png`). NEVER save screenshots to the repo root. Clean up `/tmp/` screenshot files at the end of the session.
16. **Rename, don't delete**: When replacing an existing page's content, always **rename** the original page first (e.g., `index` → `index-original`) to preserve it as a backup, then create a new page with the original name. Never delete a page unless explicitly asked to — the owner may want to reference or restore the original later.
17. **Wait after actions for snapshot**: After clicking buttons or navigating in the DA UI, add `browser_wait_for: time=2` before taking a `browser_snapshot`. The snapshot sometimes returns empty if taken too quickly after an action.
