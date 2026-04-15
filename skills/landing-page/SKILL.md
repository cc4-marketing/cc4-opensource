---
name: landing-page
description: "Generate a complete product landing page from your codebase."
argument-hint: "[deploy — to also deploy after generating]"
---

# /landing-page

Generate a complete, self-contained product landing page (single HTML file) by analyzing your codebase.

## Input

<landing_input> #$ARGUMENTS </landing_input>

## Process

### Step 1: Codebase Intake

Read these sources **in parallel** to extract product identity:

| Source | What to Extract |
|--------|----------------|
| `README.md` | Product name, description, features, install instructions |
| Package metadata* | Name, version, author, license, homepage |
| CLI help output** | Subcommands, flags, usage patterns |
| `CHANGELOG.md` | Latest version highlights |
| `Resources/` or `assets/` | App icon, screenshots |

*Package metadata: `Package.swift`, `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, `*.gemspec`

**CLI help: run the main binary with `--help` if built, or extract from README.

### Step 2: Extract Product Identity

Assemble these fields from the intake:

```yaml
product_name: ""        # From package name or README title
tagline: ""             # ≤10 words — action + where/how
expansion: ""           # 1-2 sentences — mechanism + benefit
install_command: ""     # Primary install method
features:               # Exactly 3 — most differentiating
  - title: ""
    description: ""     # One sentence, ≤20 words
demo_commands: []       # 2-4 lines showing real usage
author: ""
license: ""
repo_url: ""
homepage: ""
version: ""
```

**Present to user for review** before generating HTML.

### Step 3: Choose Design

| Product Type | Palette | Fonts |
|-------------|---------|-------|
| CLI tool | Terminal Night (dark) | Space Mono + IBM Plex Sans |
| Dev library/SDK | Deep Ocean (dark) | JetBrains Mono + Outfit |
| Desktop app | Warm Charcoal (dark) | Bricolage Grotesque + Source Sans 3 |
| Creative tool | Midnight Purple (dark) | Syne + DM Sans |
| Content tool | Warm Paper (light) | Bricolage Grotesque + Source Sans 3 |

Default to dark. Always include light mode via `@media (prefers-color-scheme: light)`.

### Step 4: Generate HTML

Single self-contained HTML file with:

**Structure (in order):**
1. **Hero** (80vh) — icon + name (gradient text) + tagline + expansion + CTA buttons
2. **Demo** — terminal window with real commands + copy button
3. **Features** — 3-card grid with icons
4. **Detail Sections** — alternating layout with code examples and checklists
5. **Getting Started** — numbered steps with terminal snippets
6. **FAQ** — accordion (6 items: free?, data?, dependencies?, etc.)
7. **Footer** — author + license + source link + version

**Technical requirements:**
- Tailwind CSS CDN (no build step)
- Google Fonts for chosen pairing
- CSS custom properties for theming
- Hero entrance animation (fadeUp keyframes)
- Terminal glow shadow
- Feature card hover lift
- FAQ accordion via `<details>` (no JS needed for that)
- Copy button JS for terminal blocks
- OG/Twitter meta tags
- Canonical URL
- Under 500 lines of HTML

**Rules:**
- All code examples must be real — copy from README or help output
- Install commands must be the actual install command
- Don't add features the product doesn't have

### Step 5: Write Output

Write to `index.html` in the project root.

If argument is `deploy`, also deploy:

```bash
# Cloudflare Pages
if npx wrangler --version &>/dev/null 2>&1; then
    mkdir -p /tmp/deploy
    cp index.html /tmp/deploy/
    [ -f og-image.png ] && cp og-image.png /tmp/deploy/
    npx wrangler pages deploy /tmp/deploy --project-name=PROJECT --branch=main --commit-dirty=true
fi
```

### Step 6: Report

```
✓ Landing page generated

  File:     index.html ({N} lines)
  Design:   {palette name} + {font pairing}
  Sections: Hero, Demo, Features (3), Detail (4), Getting Started (5 steps), FAQ (6), Footer

  Preview: open index.html
  Deploy:  /landing-page deploy
```
