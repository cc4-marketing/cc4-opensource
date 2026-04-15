# Example: Ship an Open Source Project from Scratch

A complete walkthrough using cc4-opensource + Compound Engineering skills. We'll create a simple CLI tool, plan features, build them, ship a release, market it, and document learnings.

---

## 0. Setup

```bash
# Create project
mkdir my-tool && cd my-tool && git init
echo "# my-tool\n\nA CLI that does useful things." > README.md
git add . && git commit -m "init"

# Create GitHub repo
gh repo create my-tool --public --source=. --push

# Verify skills are installed
# In Claude Code, these should all be available:
# /brainstorming /ce:plan /ce:work /release /announce /contribute /landing-page
```

---

## 1. Plan (Day 1)

### Brainstorm the idea

```
You: /brainstorming I want to build a CLI tool that converts between JSON, YAML, and TOML
```

Claude explores requirements: Who uses it? What's the primary use case? Any constraints?

You arrive at a clear design: a pipe-friendly CLI with subcommands `j2y`, `y2j`, `j2t`, `t2j`.

### Write the plan

```
You: /ce:plan convert between JSON, YAML, and TOML via CLI
```

Claude:
1. Researches your codebase (empty — new project)
2. Checks for existing solutions in `docs/solutions/`
3. Writes a phased plan to `docs/plans/2026-04-15-001-feat-format-converter-plan.md`
4. Asks: "Ready to start building?"

### Create issues and milestones

```bash
gh issue create --title "feat: JSON ↔ YAML conversion" --body "..."
gh issue create --title "feat: JSON ↔ TOML conversion" --body "..."
gh issue create --title "feat: stdin/stdout pipe support" --body "..."

gh api repos/OWNER/my-tool/milestones --method POST -f title="v0.1.0"
gh issue edit 1 --milestone "v0.1.0"
gh issue edit 2 --milestone "v0.1.0"
gh issue edit 3 --milestone "v0.1.0"
```

---

## 2. Build (Day 1-2)

### Execute the plan

```
You: /ce:work @docs/plans/2026-04-15-001-feat-format-converter-plan.md
```

Claude:
1. Creates a feature branch: `feat/format-converter`
2. Implements JSON ↔ YAML conversion
3. Adds tests
4. Commits: `feat(convert): JSON ↔ YAML via j2y and y2j subcommands`
5. Implements TOML support
6. Runs tests continuously
7. Creates PR: "feat: Format converter — JSON, YAML, TOML"

### Review

```
You: /ce:review
```

Claude runs multi-agent code review. You address any findings.

### Merge

```bash
gh pr merge 1 --squash --delete-branch
```

---

## 3. Ship (Day 2)

### Release

```
You: /release 0.1.0
```

Claude:
1. Checks tests pass ✓
2. Bumps version in `package.json` / `Cargo.toml` / etc.
3. Updates CHANGELOG.md with features from merged PRs
4. Builds release artifact
5. Tags `v0.1.0`
6. Pushes to GitHub
7. Creates release with artifact attached
8. Closes the v0.1.0 milestone

Output:
```
✓ Release v0.1.0 complete

  Tag:      v0.1.0 pushed
  Release:  https://github.com/OWNER/my-tool/releases/tag/v0.1.0
  Milestone: v0.1.0 closed
```

---

## 4. Market (Day 2-3)

### Generate landing page

```
You: /landing-page
```

Claude reads README, detects it's a CLI tool, generates a dark-themed landing page with terminal demo, feature cards, and install instructions. Writes `index.html`.

### Deploy

```
You: /landing-page deploy
```

Or manually:
```bash
npx wrangler pages deploy . --project-name=my-tool --branch=main
```

### Announce

```
You: /announce
```

Claude reads CHANGELOG.md and generates:

**Twitter (267 chars):**
```
🚀 my-tool v0.1.0

Convert between JSON, YAML, and TOML from your terminal.

• echo '{"a":1}' | my-tool j2y
• Pipe-friendly stdin/stdout
• Zero dependencies

↓ github.com/OWNER/my-tool/releases

#opensource #cli #devtools
```

**LinkedIn (full post)**
**Hacker News (Show HN format)**

You copy and post.

---

## 5. Compound (ongoing)

### Document a learning

After fixing a tricky TOML date parsing issue:

```
You: /ce:compound
```

Claude creates `docs/solutions/runtime-errors/toml-date-parsing-rfc3339-20260415.md` documenting the root cause, fix, and prevention strategy.

### Onboard contributors

```
You: /contribute
```

Claude analyzes the project:
```
Detected:
  Language:  Rust 1.75
  Build:     cargo build
  Test:      cargo test
  Commits:   conventional (feat/fix/docs)
  Linter:    clippy
```

Writes `CONTRIBUTING.md` with setup instructions, commit conventions, PR checklist, and architecture overview.

---

## Result

After 2-3 days you have:
- A working CLI tool with tests
- A GitHub release with artifact
- A landing page
- Social media announcements
- CONTRIBUTING.md for future contributors
- Documented solutions for problems you solved

Next version? Start at Phase 1 again:
```
/brainstorming → /ce:plan → /ce:work → /release → /announce
```

---

## Command Cheat Sheet

```bash
# Plan
/brainstorming                  # Explore what to build
/ce:plan                        # Write implementation plan

# Build
/ce:work @plan.md              # Execute the plan
/ce:review                      # Code review

# Ship
/release minor                  # Full release pipeline

# Market
/announce                       # Social posts from changelog
/landing-page deploy            # Generate + deploy landing page

# Compound
/ce:compound                    # Document a solved problem
/contribute                     # Generate CONTRIBUTING.md
```
