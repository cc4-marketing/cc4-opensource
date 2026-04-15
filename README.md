# cc4-opensource

Slash commands for the open source software lifecycle. Plan, build, ship, market, and document — all from Claude Code.

**By [cc4.marketing](https://cc4.marketing)**

---

## The Problem

Shipping open source software involves more than writing code. You need to version bump, build artifacts, sign releases, write changelogs, create GitHub releases, update landing pages, generate social announcements, and maintain contributor docs. Each step is simple but the pipeline is 10+ manual commands that are easy to forget or do out of order.

## The Solution

Four slash commands that fill the gaps in the Claude Code + Compound Engineering workflow:

```bash
/release 1.5.0    # Version bump → build → sign → tag → GH release → deploy
/announce         # Generate Twitter, LinkedIn, HN posts from CHANGELOG
/landing-page     # Generate product landing page from codebase
/contribute       # Generate CONTRIBUTING.md from codebase analysis
```

These plug into the existing Compound Engineering skills to create a complete pipeline:

```
/brainstorming → /ce:plan → /ce:work → /release → /announce
                                          ↓
                                    /landing-page
```

---

## Quick Start

### 1. Install

```bash
git clone https://github.com/cc4-marketing/cc4-opensource.git
mkdir -p ~/.claude/skills
ln -s $(pwd)/cc4-opensource/skills/release ~/.claude/skills/release
ln -s $(pwd)/cc4-opensource/skills/announce ~/.claude/skills/announce
ln -s $(pwd)/cc4-opensource/skills/contribute ~/.claude/skills/contribute
ln -s $(pwd)/cc4-opensource/skills/landing-page ~/.claude/skills/landing-page
```

### 2. Also install [Compound Engineering](https://github.com/anthropics/claude-code) plugin

```bash
/install-plugin compound-engineering
```

### 3. Use

```bash
# Ship a release
/release 1.5.0

# Tell people about it
/announce

# Generate a landing page
/landing-page

# Onboard contributors
/contribute
```

See [docs/installation.md](docs/installation.md) for the full setup guide.

---

## Workflow

Five phases, each with dedicated skills:

### Phase 1: Plan

| What | Command | From |
|------|---------|------|
| Explore ideas | `/brainstorming` | CE plugin |
| Write plan | `/ce:plan` | CE plugin |
| Add depth | `/deepen-plan` | CE plugin |
| Triage bugs | `/triage` | CE plugin |

### Phase 2: Build

| What | Command | From |
|------|---------|------|
| Execute plan | `/ce:work @plan.md` | CE plugin |
| Fast mode | `/lfg` | CE plugin |
| Code review | `/ce:review` | CE plugin |
| Parallel dev | `/git-worktree` | CE plugin |

### Phase 3: Ship

| What | Command | From |
|------|---------|------|
| Full release | **`/release 1.5.0`** | **cc4-opensource** |
| Changelog | `/changelog` | CE plugin |
| Deploy docs | `/deploy-docs` | CE plugin |

### Phase 4: Market

| What | Command | From |
|------|---------|------|
| Announcements | **`/announce`** | **cc4-opensource** |
| Landing page | **`/landing-page`** | **cc4-opensource** |
| OG image | `/gemini-imagegen` | CE plugin |
| Feature video | `/feature-video` | CE plugin |
| Screenshots | `/agent-browser` | CE plugin |

### Phase 5: Compound

| What | Command | From |
|------|---------|------|
| Document fix | `/ce:compound` | CE plugin |
| Contributor guide | **`/contribute`** | **cc4-opensource** |

---

## Skill Details

### `/release`

Automates the complete release pipeline:

```
Pre-flight checks → Version bump → Changelog → Build → Sign →
Tag → Push → GH Release → Deploy site → Close milestone
```

- Auto-detects project type (Swift, Node, Rust, Python, Ruby, Go)
- Finds and updates ALL version strings across the project
- Signs with Sparkle EdDSA if configured (macOS apps)
- Deploys landing page via Cloudflare Pages if configured
- Closes the GitHub milestone

```bash
/release              # Interactive — asks for version
/release 1.5.0        # Specific version
/release patch        # 1.4.0 → 1.4.1
/release minor        # 1.4.0 → 1.5.0
```

### `/announce`

Generates platform-specific announcements from CHANGELOG:

- **Twitter/X** — under 280 chars, 3 bullets, hashtags
- **LinkedIn** — 300-500 words, professional tone
- **Hacker News** — Show HN format with technical details
- **GitHub Discussion** — full release notes with install instructions
- **Newsletter** — email-friendly format

```bash
/announce             # Latest version, all platforms
/announce 1.4.0       # Specific version
/announce x           # Twitter only
```

### `/landing-page`

Generates a complete product landing page:

- Hero with gradient text + animated icon
- Terminal demo with real commands
- 3-feature card grid
- Detail sections with code examples
- Getting started guide (numbered steps)
- FAQ accordion
- Light/dark mode
- OG + Twitter meta tags
- Single HTML file, Tailwind CDN, no build step

```bash
/landing-page         # Generate index.html
/landing-page deploy  # Generate + deploy to Cloudflare Pages
```

### `/contribute`

Generates CONTRIBUTING.md by reading your codebase:

- Detects language, build/test commands, commit conventions
- Detects linters, formatters, CI
- Includes architecture overview from README
- Verifies all commands work before writing

```bash
/contribute           # Generate CONTRIBUTING.md
/contribute update    # Update existing file
```

---

## Example: Full Lifecycle

Real workflow from the [Clip](https://github.com/blacklogos/markutils) project (macOS menu bar app):

```bash
# Plan: 5 issues → 4 milestones → phased plan
/ce:plan "view all issues and plan"
# → docs/plans/2026-04-09-001-feat-v1.4-plan.md

# Build: one branch per phase, PR per phase
/ce:work @docs/plans/2026-04-09-001-feat-v1.4-plan.md
# → 4 PRs merged: search, markdown, images, sparkle

# Ship: version bump + DMG + sign + release
/release 1.4.0
# → Clip-1.4.0.dmg signed + uploaded + appcast updated

# Market: announcements + landing page
/announce
# → Twitter, LinkedIn, HN posts generated
/landing-page deploy
# → clip.cc4.marketing updated

# Compound: document the Sparkle @rpath fix
/ce:compound
# → docs/solutions/build-errors/sparkle-rpath.md
```

---

## File Structure

```
cc4-opensource/
├── README.md                      # This file
├── CLAUDE.md                      # Claude Code project instructions
├── LICENSE                        # MIT
├── skills/
│   ├── release/SKILL.md           # /release — full release pipeline
│   ├── announce/SKILL.md          # /announce — social announcements
│   ├── contribute/SKILL.md        # /contribute — CONTRIBUTING.md generator
│   └── landing-page/SKILL.md      # /landing-page — product landing page
├── docs/
│   ├── installation.md            # Setup guide (CE plugin + these skills + gh + wrangler)
│   ├── workflow.md                # Complete 5-phase lifecycle reference
│   └── quick-reference.md         # One-page cheat sheet
└── templates/
    ├── CONTRIBUTING.md             # Ready-to-use contributing template
    ├── ISSUE_TEMPLATE.md           # GitHub issue template
    ├── PULL_REQUEST_TEMPLATE.md    # GitHub PR template
    └── CHANGELOG-ENTRY.md          # Changelog section template
```

---

## Requirements

| Tool | Required | Used By |
|------|----------|---------|
| [Claude Code](https://claude.ai/code) | Yes | All skills |
| [Compound Engineering plugin](https://github.com/anthropics/claude-code) | Yes | Plan/Build/Review/Compound phases |
| [GitHub CLI (`gh`)](https://cli.github.com/) | Yes | `/release`, `/announce` |
| [Wrangler](https://developers.cloudflare.com/workers/wrangler/) | Optional | `/release` deploy, `/landing-page deploy` |
| [Sparkle](https://sparkle-project.org/) | Optional | `/release` EdDSA signing (macOS apps) |

---

## Language Support

Skills auto-detect your project type:

| Language | Build | Test | Package File |
|----------|-------|------|-------------|
| Swift | `swift build` | `swift test` | `Package.swift` |
| Node.js | `npm run build` | `npm test` | `package.json` |
| Rust | `cargo build --release` | `cargo test` | `Cargo.toml` |
| Python | `python -m build` | `pytest` | `pyproject.toml` |
| Ruby | `gem build` | `bundle exec rake test` | `Gemfile` |
| Go | `go build` | `go test ./...` | `go.mod` |

---

## License

MIT

---

**[cc4.marketing](https://cc4.marketing)** — tools for builders who ship.
