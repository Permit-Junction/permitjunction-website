# Permit Junction Website

This repository powers **[www.permitjunction.com](https://www.permitjunction.com/)**, built on **AEM Edge Delivery Services** (Author Kit). The site uses a buildless architecture: blocks are vanilla JavaScript/CSS modules loaded at runtime—no compile step required.

---

## Who should read what

| You are a…         | Start here |
|--------------------|------------|
| **Content creator** | [Content creators: editing in DA](#content-creators-editing-in-da) |
| **Developer**       | [Developers: local dev & shipping](#developers-local-dev--shipping) |
| **Anyone using AI** | [Using the Claude Code agent](#using-the-claude-code-agent) |

---

## Required tools

| Tool | Install | Docs |
|------|---------|------|
| **Google Chrome** | [Download](https://www.google.com/chrome/) | — |
| **AEM Sidekick** (browser extension) | [Chrome Web Store](https://chromewebstore.google.com/detail/aem-sidekick/igkmdomcgoebiipaifhmpfjhbjccggml) | [Sidekick docs](https://www.aem.live/docs/sidekick) |

---

## Important links

| Purpose | URL |
|---------|-----|
| **DA editor** (edit content) | <https://da.live/edit#/permit-junction/permitjunction-website> |
| **DA file browser** | <https://da.live/#/permit-junction/permitjunction-website> |
| **Preview (main branch)** | <https://main--permitjunction-website--permit-junction.aem.page/> |
| **Live origin (published)** | <https://main--permitjunction-website--permit-junction.aem.live/> |
| **Branch preview pattern** | `https://{branch}--permitjunction-website--permit-junction.aem.page/{path}` |
| **Public site (CDN)** | <https://www.permitjunction.com/> |

---

## Official documentation (further reading)

- **DA (Document Authoring)**: <https://docs.da.live>
- **AEM Edge Delivery Services**: <https://www.aem.live/docs>
- **AEM developer docs**: <https://www.aem.live/developer/>
- **Performance best practices ("Keeping it 100")**: <https://www.aem.live/developer/keeping-it-100>
- **Block Collection (reference implementations)**: <https://www.aem.live/developer/block-collection>

---

## Content creators: editing in DA

### Where you edit

All page content lives in **DA (Document Authoring)** at <https://da.live/edit#/permit-junction/permitjunction-website>.

### How pages are structured

- **Sections** are separated by horizontal rules (section breaks).
- **Blocks** appear as tables with a colored title row (e.g., `hero`, `card`).
- **Section metadata** is a special block at the end of a section that controls layout (grid, spacing, background, etc.).

### How to preview

1. **In DA**: Click the blue paper-airplane icon → **Preview**. This opens a `.aem.page` URL in a new tab showing your latest changes.
2. **In browser**: Use the AEM Sidekick toolbar → **Preview** or **Update** to refresh.

### Publishing

- **Preview is safe**—it only affects the preview environment.
- **Publish updates what visitors see** on the live site.
- If you are not an authorized operator, **request publish** from someone who is.

---

## Developers: local dev & shipping

### Prerequisites

- Node.js / npm
- AEM CLI: `npm install -g @adobe/aem-cli`
- (Recommended) Chrome + AEM Sidekick for testing against preview/live

### Local development

```bash
# Install dependencies
npm install

# Start local dev server (serves at http://localhost:3000)
aem up

# Linting
npm run lint        # JS + CSS
npm run lint:js     # ESLint only
npm run lint:css    # Stylelint only

# Testing
npm test            # All tests with coverage
npm run test:watch  # Watch mode
```

### Repo map (where to change things)

| What | Location | Notes |
|------|----------|-------|
| Blocks | `blocks/` | Each block has `{name}.js` + `{name}.css` |
| Site entry/config | `scripts/scripts.js` | Hostnames, locales, link-block patterns |
| Core framework | `scripts/ak.js` | **Do not modify**—this is the shared AEM framework |
| Global styles/tokens | `styles/` | Design tokens, base element styles |

### Shipping changes

1. Create a feature branch and push your changes.
2. AEM Code Sync automatically generates a branch preview: `https://{branch}--permitjunction-website--permit-junction.aem.page/{path}`
3. Open a PR to `main`. **Include at least one preview link** in the PR description (required for automated checks).
4. After review, merge to `main`. An operator can then publish content to make changes live.

---

## Using the Claude Code agent

This repo is configured for **Claude Code**, an AI coding agent that can help both content creators and developers.

### What the agent can do for content creators

- Rewrite, shorten, or expand copy on a specific DA page
- Draft new sections, headings, or CTAs
- Suggest block structures (tables) and walk you through authoring steps
- Navigate DA, make edits, and trigger **Preview** (if browser automation is enabled)

### What the agent can do for developers

- Implement or modify blocks (JS/CSS), update global styles, fix bugs
- Run lint and tests, troubleshoot errors
- Generate PR-ready commit messages and ensure preview links are included
- Use browser automation to visually verify pages (screenshots, responsive checks)

### What the agent will NOT do by default

- **Publish** content unless you explicitly ask and confirm
- **Commit or push** changes unless you explicitly ask
- Add secrets to the repo—keep tokens in local-only files (`.claude/settings.local.json`, `.mcp.json`)

### How to ask effectively

**Content example:**
> Update this DA page `https://da.live/edit#/permit-junction/permitjunction-website/services` with these copy changes: … then preview (don't publish).

**Code example:**
> Change the `hero` block to support a new `compact` variant. Test on `/drafts/test-hero`. Run `npm run lint` when done.

### Repo-specific agent features

- **Skills** (specialized workflows) live in `.claude/skills/`. Key ones:
  - `/da-content-authoring` — content editing in DA
  - `/content-driven-development` — code changes with content-first validation
  - `/testing-blocks` — testing and PR prep
- **Project settings** are in `.claude/settings.json`.

### Claude Code documentation

- Setup: <https://code.claude.com/docs/en/setup>
- Quickstart: <https://docs.anthropic.com/en/docs/claude-code/quickstart>

---

## License

Apache License 2.0 — see [LICENSE](LICENSE).
