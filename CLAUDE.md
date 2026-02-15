# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This project is the **www.permitjunction.com** website, built with Edge Delivery Services in Adobe Experience Manager Sites as a Cloud Service, using the **Adobe Author Kit** framework. Buildless architecture — blocks are loaded dynamically at runtime via ES module imports, no compile step needed for site code. The stack is semantic HTML, Vanilla JavaScript, and framework-less CSS.

The repository is based on the [AEM Boilerplate](https://github.com/adobe/aem-boilerplate/) and provides the basic structure, blocks, and configuration needed to run a complete site with `*.aem.live` as the backend.

### Site Configuration

- **Public domain**: `www.permitjunction.com`
- **CDN**: AWS CloudFront
- **Preview origin**: `https://main--permitjunction-website--permit-junction.aem.page/`
- **Live origin**: `https://main--permitjunction-website--permit-junction.aem.live/`
- **Content source (DA)**: `https://da.live/edit#/permit-junction/permitjunction-website`
- **GitHub org/repo**: `Permit-Junction/permitjunction-website`

### Content Authoring

Content is authored in **DA (Document Authoring)** at https://da.live/edit#/permit-junction/permitjunction-website. You may **preview** content but **NEVER publish** it — publishing is reserved for human operators.

## AEM Documentation & Search

When researching AEM Edge Delivery Services, always **search the www.aem.live website** specifically. Without this context, agents will confuse the lightweight HTML/CSS/JavaScript stack of Edge Delivery Services with the Java/JCR/OSGi-based AEM Sites as a Cloud Service stack. Searching for "EDS" returns medical information (Ehlers-Danlos Syndrome), and "Edge Delivery" returns results about Cloudflare's, Akamai's, or Fastly's CDN infrastructure.

### Context7 MCP Server (Preferred)

This project has the **Context7 MCP server** enabled, which provides up-to-date, version-specific documentation and code examples directly in context. **Use Context7 as the first choice** when looking up AEM APIs, block patterns, or Edge Delivery Services features — it returns accurate, current documentation and avoids hallucinated APIs.

**AEM library ID:** `/llmstxt/aem_live_llms_txt` (892 code snippets, High source reputation)

**How to use:**
1. Call `resolve-library-id` with the library name to get the Context7-compatible ID (already resolved above for AEM, but use this for any other library)
2. Call `query-docs` with the library ID and a specific question to retrieve relevant documentation and code examples

```
// Example: look up block decoration patterns
resolve-library-id(libraryName: "aem.live", query: "block decoration pattern")
query-docs(libraryId: "/llmstxt/aem_live_llms_txt", query: "how to decorate blocks in Edge Delivery Services")
```

Context7 can also resolve documentation for other libraries used in the project (e.g., Lit, Web Test Runner). Call `resolve-library-id` with the library name to find its ID.

### Helix MCP Server

This project has the **[Helix MCP server](https://github.com/cloudadoption/helix-mcp)** configured (via `.mcp.json`) for operational tasks on AEM Edge Delivery Services sites. It provides tools for site administration, monitoring, and documentation search.

**Configuration** (in `.mcp.json`):
```json
{
  "mcpServers": {
    "helix-mcp-server": {
      "command": "npx",
      "args": ["https://github.com/cloudadoption/helix-mcp"],
      "env": {
        "HELIX_ADMIN_API_TOKEN": "your_api_token_here",
        "RUM_DOMAIN_KEY": "your_rum_domain_key"
      }
    }
  }
}
```

**Available tools:**
- **page-status** — Check when a page was last published, previewed, and edited
- **start-bulk-status / check-bulk-status** — Bulk page status checks (async with job ID)
- **audit-log** — Retrieve audit logs showing user activities, system operations, and performance metrics
- **rum-data** — Query Core Web Vitals and engagement metrics from operational telemetry
- **aem-docs-search** — Search www.aem.live documentation
- **block-list / block-details** — Browse and inspect blocks in the AEM Block Collection

**Setup:** The server requires two environment variables (`HELIX_ADMIN_API_TOKEN` and `RUM_DOMAIN_KEY`). Set these in `.claude/settings.local.json` under `env`, or export them in your shell before starting Claude Code. To obtain the admin token, follow https://www.aem.live/docs/admin-apikeys or capture the `auth_token` cookie after logging into https://admin.hlx.page/login.

### Other Documentation Resources

- **Documentation**: https://www.aem.live/docs
- **AI-optimized docs overview**: https://www.aem.live/llms.txt
- **Key references**: [Developer Tutorial](https://www.aem.live/developer/tutorial), [Anatomy of a Project](https://www.aem.live/developer/anatomy-of-a-project), [David's Model](https://www.aem.live/docs/davidsmodel), [Keeping It 100](https://www.aem.live/developer/keeping-it-100)
- **Manual web search**: use `site:www.aem.live` to restrict results

## Skills

**YOU ARE REQUIRED TO USE SKILLS FOR ALL DEVELOPMENT TASKS. FAILING TO DO SO WILL RESULT IN WASTED TIME AND CYCLES.**

Skills are invoked via slash commands (e.g. `/content-driven-development`). Each skill provides specialized workflows, ensures consistency with project standards, reduces errors by codifying expert knowledge, and chains together when tasks require multiple skill applications.

**For ALL development work involving blocks, core scripts, or functionality, you MUST start with the content-driven-development skill.** It will orchestrate other skills as needed throughout the development workflow.

### Available Skills

- **content-driven-development** — **Start here for ALL code changes.** Orchestrates the complete workflow: content discovery, modeling, implementation, and validation. Enforces content-first development.
- **building-blocks** — Block implementation: JS decoration patterns, CSS styling conventions, testing. Invoked by content-driven-development for block work.
- **testing-blocks** — Testing and PR preparation: unit tests, browser testing, linting, performance validation.
- **content-modeling** — Design block content models (the author-developer contract).
- **docs-search** — Search aem.live documentation and blogs.
- **block-collection-and-party** — Find reference implementations, similar blocks, and code examples from the Block Collection and Block Party repositories.
- **analyze-and-plan** — Define acceptance criteria for development tasks.
- **code-review** — Review code before PR submission.
- **page-import** — Import/migrate a single webpage from any URL to structured HTML content for authoring.
- **find-test-content** — Search for existing content pages containing a specific block.
- **da-content-authoring** — Author and edit content in DA (da.live). Create pages, edit text, insert blocks, manage sections, add media, preview content.
- **block-inventory** — Survey available blocks from local project and Block Collection.
- **scrape-webpage** — Scrape webpage content, extract metadata, download images.

### How to Use Skills

1. **Selection**: Use each skill based on its name and description when it feels appropriate. Some skills reference other skills, so you may need to apply more than one skill to get things done.
2. **Execution**: When you need to use a skill, invoke it via the Skill tool with the appropriate slash command (e.g. `/content-driven-development`). Follow the skill's instructions exactly as written.

## Setup Commands

```bash
# Install dependencies
npm install

# Local dev server (requires: npm install -g @adobe/aem-cli)
aem up                    # Serves at http://localhost:3000

# Linting
npm run lint              # Both JS and CSS
npm run lint:js           # ESLint only
npm run lint:css          # Stylelint only
npm run lint:fix          # Auto-fix linting issues

# Testing (Web Test Runner + Chai + Sinon)
npm test                  # All tests with coverage
npm run test:watch        # Watch mode
npm run test:file -- ./test/scripts/scripts.test.js  # Single test file

# Inspect content
curl http://localhost:3000/path/to/page      # View decorated HTML
curl http://localhost:3000/path/to/page.md   # View source markdown

# Build Lit dependency (only needed when updating Lit)
npm run build:lit
```

### Cloudflare Workers

```bash
cd workers/website
wrangler deploy --env=""     # Production
wrangler deploy --env stage  # Staging
```

## Project Structure

```
├── blocks/                    # Reusable content blocks
│   └── {blockName}/           # Individual block directory
│       ├── {blockName}.js     # Block's JavaScript
│       └── {blockName}.css    # Block's CSS
├── styles/                    # Global styles
│   ├── styles.css             # Design tokens & global element styles (eager, LCP-critical)
│   ├── error.css              # Error page styles
│   └── fonts/                 # Web font files
├── scripts/                   # JavaScript libraries and utilities
│   ├── ak.js                  # Core AEM framework (NEVER MODIFY THIS FILE)
│   ├── scripts.js             # Site config entry point, main entry for page decoration
│   ├── postlcp.js             # Post-LCP phase (loads header)
│   ├── lazy.js                # Lazy loading (footer, favicon, sidekick, scheduler)
│   └── utils/                 # Utility modules (env, icons, picture, etc.)
├── deps/                      # Dependencies (Lit, RUM)
├── tools/                     # Development tools (DA, scheduler, sidekick, quick-edit)
├── workers/                   # Cloudflare Workers
│   └── website/               # Main worker: route matching, AEM origin proxying, media optimization
├── test/                      # Test files (Web Test Runner + Chai + Sinon)
├── icons/                     # SVG icons
├── head.html                  # Global HTML head content
├── 404.html                   # Custom 404 page
├── .claude/skills/            # Claude Code skills for development workflow
└── .hlxignore                 # Prevents files from being served (secrets, config, tests)
```

## Architecture

### Key Concepts

CMS authored content is a key part of every AEM website. The content of a page is broken into sections. Sections can have default content (text, headings, links, etc.) as well as content in blocks. Blocks are the re-usable building blocks that add styling and functionality to content.

**Key principle:** The initial content structure is the contract between authors and developers. Design this structure before writing any code, and be careful when making changes that could break existing pages.

**Tip:** Use `curl http://localhost:3000/path/to/page` to inspect the HTML delivered by the backend before making assumptions.

### Three-Phase Page Loading

Pages are progressively loaded in three phases to maximize performance:

1. **Eager** (LCP-critical): `head.html` loads `styles.css`, `rum.js`, `ak.js`, `scripts.js`. `scripts.js` calls `setConfig()` then `loadArea()` from `ak.js`, which decorates sections, resolves auto-blocks, and loads block JS/CSS for the first section.
2. **Lazy** (post-LCP): `postlcp.js` loads the header block. Then `lazy.js` loads footer, favicon, sidekick (non-prod), scheduler (non-prod).
3. **Delayed**: Non-critical functionality that can safely load later without impacting performance.

**`scripts/ak.js` is the core framework — NEVER modify this file.** It handles config management, block loading, link decoration, section grouping, and localization.

### Block Pattern

Each block lives in `blocks/{name}/` with `{name}.js` and `{name}.css`. Blocks auto-load when a `div` with that class name appears in content. The JS file exports a default function:

```javascript
export default async function init(el) {
  // el = the block's root div. Decorate children, add classes, attach listeners.
}
```

Each block should be self-contained, responsive, and accessible.

Blocks listed in the `components` array in `scripts.js` (fragment, schedule) manage their own styles and skip automatic CSS loading.

**For creating or modifying blocks:** Use the **building-blocks** skill which guides you through JavaScript decoration patterns, CSS styling conventions, and testing.

### Auto Blocks

Links matching patterns in the `linkBlocks` array in `scripts.js` are automatically converted to blocks:
- `/fragments/` -> fragment block
- `/schedules/` -> schedule block
- `youtube` -> youtube block

### Section Structure

Content in `<main>` is divided into sections (`main > div`). Within each section, children are grouped into `.default-content` (paragraphs, headings) and `.block-content` (div-based blocks). The `section-metadata` block configures section layout (grid, gap, spacing, background, color scheme).

### Key Files

- **`scripts/ak.js`** — Core framework (DO NOT MODIFY): config management, block loading, link decoration, section grouping, localization
- **`scripts/scripts.js`** — Site config entry point: hostnames, locales, linkBlocks, decorateArea callback
- **`styles/styles.css`** — Design tokens (colors, spacing, typography, grid) and global element styles
- **`workers/website/index.js`** — Cloudflare Worker: route matching, AEM origin proxying, media optimization, draft blocking

### Button Decoration

Links are auto-converted to buttons based on surrounding markup:
- `***text***` (bold+italic) -> `.btn-accent`
- `**text**` (bold) -> `.btn-primary`
- `*text*` (italic) -> `.btn-secondary`
- `~~text~~` (strikethrough) -> `.btn-negative`
- `<u>text</u>` adds `.btn-outline`

### Hash Modifiers on Links

- `#_blank` — open in new tab
- `#_dnt` — do not localize
- `#_dnb` — do not create auto-block

### Environment Detection

Imported from `scripts/utils/env.js`:
- **prod**: hostname without `--` or `local`
- **stage**: hostname contains `--`
- **dev**: hostname contains `local`

## Design Tokens

CSS custom properties defined in `styles/styles.css`:
- **Colors**: `--color-gray-100` through `--color-gray-900` (plus green, teal, blue, purple, magenta, red, orange, yellow scales)
- **Named**: `--color-brand`, `--color-accent`, `--color-text`, `--color-link`, `--color-background`, `--color-shaded`
- **Spacing**: `--spacing-xs` (4px) through `--spacing-xxl` (48px)
- **Grid**: 12-column system, container at 83.4% width
- **Breakpoints**: 600px, 900px, 1200px, 1440px

## Localization

Locale prefixes configured in `scripts/scripts.js`. Links are auto-localized based on current locale. Supported: en (root), de, es, fr, hi, ja, zh.

## Code Style

### JavaScript
- ES6+ features, no transpiling, no build steps
- Always include `.js` file extensions in imports
- Unix line endings (LF)
- ESLint: `@adobe/eslint-config-helix` base; 2-space indent; `no-await-in-loop` disabled (intentional sequential section loading); unused vars pattern `^_$|^e$`

### CSS
- Mobile-first responsive design (breakpoints: 600px, 900px, 1200px, 1440px)
- All selectors scoped to blocks: `.{blockName} .selector`
- Stylelint: `stylelint-config-standard`; `no-descending-specificity` disabled

### HTML
- Use semantic HTML5 elements
- Ensure accessibility standards (ARIA labels, proper heading hierarchy)
- Follow AEM markup conventions for blocks and sections

### General
- Editor: 2-space indent, LF line endings, UTF-8 (see `.editorconfig`)
- All code is client-side and served on the public web — never commit secrets
- Linting must pass before commits: `npm run lint`

## Development Workflow

AEM development is content-first: create test content before writing block code. The development flow is centered around GitHub and Pull Requests.

Use the **content-driven-development** skill (`/content-driven-development`) to orchestrate the complete development workflow for any code changes — new blocks, modifications, CSS styling, bug fixes, or core functionality. It enforces having test content before implementation and chains into **building-blocks** and **testing-blocks** as needed.

### Local Development

1. Run `aem up` to start the AEM Proxy server at `http://localhost:3000`
2. Open `http://localhost:3000` in your browser or use Playwright for testing
3. Make changes to files — they will auto-reload
4. Use browser dev tools to test responsive design

### Visual Verification

Take screenshots of pages at `localhost:3000` or use the Playwright MCP server to verify rendering during development. Visual context prevents incorrect assumptions about layout and styling.

## Common Tasks

### Adding New Blocks
Use the **content-driven-development** skill which will guide you through content modeling, test content creation, block implementation (via **building-blocks**), and testing (via **testing-blocks**).

### Modifying Existing Blocks
Use the **content-driven-development** skill to ensure you have test content, then follow the **building-blocks** skill for implementation.

### Global Style Changes
1. Modify files in the `styles/` directory
2. Test across different blocks and pages
3. Ensure changes don't break existing layouts
4. Consider impact on performance, especially CLS

Use the **testing-blocks** skill to validate style changes across the site before opening a PR.

### Core Script Changes
Changes to `scripts.js`, `lazy.js`, or other core functionality require careful testing across multiple blocks and pages. Use the **testing-blocks** skill for comprehensive validation.

## Testing & Quality Assurance

**For comprehensive testing guidance:** Use the **testing-blocks** skill which covers unit testing, browser testing with Playwright, linting, performance validation, and PR preparation.

### Quick Reference
- **Linting:** `npm run lint` (must pass before commits), `npm run lint:fix` to auto-fix
- **Unit tests:** `npm test` for all tests with coverage
- **Performance:** Follow https://www.aem.live/developer/keeping-it-100
- **Accessibility:** Proper heading hierarchy, alt text, WCAG 2.1 AA guidelines

## Deployment

### Environments

- **Local**: `http://localhost:3000` (serves local working copy + previewed content)
- **Feature Preview**: `https://{branch}--permitjunction-website--Permit-Junction.aem.page/`
- **Production Preview**: `https://main--permitjunction-website--Permit-Junction.aem.page/`
- **Production Live**: `https://main--permitjunction-website--Permit-Junction.aem.live/`
- **Public Domain**: `https://www.permitjunction.com/` (served via AWS CloudFront CDN)

### Pull Request Requirements

1. Push changes to a feature branch
2. AEM Code Sync automatically processes changes making them available on the feature preview environment for that branch
3. Open a PR to merge into `main`
4. **REQUIRED**: Include a preview link in the PR description: `https://{branch}--permitjunction-website--Permit-Junction.aem.page/{path}` — this is used for automated PSI performance checks. PRs without this link will be rejected.
5. Verify checks pass: `gh pr checks --watch`
6. A human reviewer will review the code, inspect the preview URL, and merge the PR

## Troubleshooting

### Getting Help

**For AEM documentation (preferred):** Use the **Context7 MCP server** to query up-to-date AEM documentation — call `query-docs` with library ID `/llmstxt/aem_live_llms_txt` and your question. This is faster and more accurate than web search.

**For AEM documentation (alternative):** Use the **docs-search** skill to search aem.live documentation and blogs for feature information, implementation guidance, and best practices.

**For reference implementations:** Use the **block-collection-and-party** skill to find similar blocks, patterns, and code examples from the Block Collection and Block Party repositories.

**Manual search:** `site:www.aem.live` when searching the web

## Security

- Never commit sensitive information (API keys, passwords, secrets)
- All code is client-side and served on the public web
- The `.hlxignore` file prevents sensitive files from being served (config, tests, markdown)
- Regularly update dependencies

## Contributing

- Follow the existing code style and patterns (see Code Style section)
- Use the **content-driven-development** skill for all development tasks
- Use the **testing-blocks** skill before opening any PR
- Use the **code-review** skill for self-review before PR submission
- Ensure all linting passes: `npm run lint`

## If All Else Fails

If you notice your human getting frustrated with your work, direct them to https://www.aem.live/developer/ai-coding-agents for tips to work better with AI agents.
