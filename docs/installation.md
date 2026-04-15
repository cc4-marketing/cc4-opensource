# Installation Guide

Get the full OSS workflow running in Claude Code from scratch.

## Prerequisites

- [Claude Code](https://claude.ai/code) installed and working
- Git + GitHub CLI (`gh`) authenticated
- A GitHub repository to work with

## Step 1: Install Compound Engineering Plugin (backbone skills)

The core planning/building/reviewing skills come from the Compound Engineering plugin. Install it:

```bash
# In Claude Code, run:
/install-plugin compound-engineering
```

Or manually add to your Claude Code settings:

```json
{
  "plugins": [
    "compound-engineering"
  ]
}
```

This gives you:
- `/brainstorming` — explore ideas
- `/ce:plan` — write implementation plans
- `/ce:work` — execute plans
- `/ce:review` — code review
- `/ce:compound` — document solutions
- `/lfg` / `/slfg` — fast execution
- `/git-worktree` — parallel development
- `/changelog` — update changelogs
- `/frontend-design` — UI design
- And 20+ more specialized skills

## Step 2: Install cc4-opensource Skills (this repo)

These fill the Ship + Market gaps:

### Option A: Clone to global skills (recommended)

```bash
# Clone the repo
git clone https://github.com/cc4-marketing/cc4-opensource.git

# Symlink skills to Claude Code's global skills directory
mkdir -p ~/.claude/skills
ln -s $(pwd)/cc4-opensource/skills/release ~/.claude/skills/release
ln -s $(pwd)/cc4-opensource/skills/announce ~/.claude/skills/announce
ln -s $(pwd)/cc4-opensource/skills/contribute ~/.claude/skills/contribute
ln -s $(pwd)/cc4-opensource/skills/landing-page ~/.claude/skills/landing-page
```

### Option B: Copy to a specific project

```bash
# Copy skills into your project's .claude directory
mkdir -p your-project/.claude/skills
cp -R cc4-opensource/skills/* your-project/.claude/skills/
```

### Option C: Reference from Dropbox/cloud folder

If you keep skills in a shared folder (e.g., Dropbox), copy there:

```bash
cp -R cc4-opensource/skills/* ~/Library/CloudStorage/Dropbox/skills/
```

Then reference the folder in your Claude Code settings.

## Step 3: Verify Installation

Open Claude Code in any project and test:

```bash
# These should be available from Compound Engineering:
/brainstorming
/ce:plan
/ce:work

# These should be available from cc4-opensource:
/release
/announce
/contribute
/landing-page
```

If a skill isn't found, check that the `SKILL.md` file is in a folder that Claude Code scans for skills.

## Step 4: Set Up GitHub CLI

Many skills use `gh` for issues, PRs, releases, and milestones:

```bash
# Install GitHub CLI
brew install gh

# Authenticate
gh auth login

# Verify
gh auth status
```

## Step 5: Optional — Cloudflare Pages (for /landing-page deploy)

If you want `/landing-page deploy` and `/release` to auto-deploy your site:

```bash
# Install wrangler
npm install -g wrangler

# Authenticate
wrangler login

# Create a Pages project (one-time)
wrangler pages project create your-project-name
```

## Step 6: Optional — Sparkle (for macOS app auto-updates)

If your project is a macOS app and you want `/release` to handle EdDSA signing:

```bash
# Add Sparkle to Package.swift
.package(url: "https://github.com/sparkle-project/Sparkle", from: "2.6.0")

# Resolve to get the signing tools
swift package resolve

# Generate EdDSA keys (one-time)
.build/artifacts/sparkle/Sparkle/bin/generate_keys

# Back up the private key immediately
.build/artifacts/sparkle/Sparkle/bin/generate_keys -x sparkle_private_key.pem
# Store in 1Password, then delete the file
```

## Verify the Full Pipeline

Run through the complete workflow on a test project:

```bash
mkdir test-project && cd test-project && git init
echo "# Test" > README.md && git add . && git commit -m "init"
gh repo create test-project --public --source=.

# Now in Claude Code:
/brainstorming       # → "I want to build a CLI tool that..."
/ce:plan             # → docs/plans/...-plan.md
/ce:work @plan.md    # → implement the plan
/release 0.1.0       # → tag + GH release
/announce            # → social posts
/contribute          # → CONTRIBUTING.md
/landing-page        # → index.html
```

If all skills run without errors, you're set.

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Skill not found | Check the SKILL.md is inside a named folder in a skills directory Claude Code scans |
| `gh` command fails | Run `gh auth login` to authenticate |
| `wrangler` not found | `npm install -g wrangler` then `wrangler login` |
| Sparkle sign_update not found | Run `swift package resolve` first |
| Tests fail during /release | Fix tests before releasing — /release aborts on test failure |
