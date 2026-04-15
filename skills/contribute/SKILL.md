---
name: contribute
description: "Generate or update CONTRIBUTING.md by analyzing codebase conventions."
argument-hint: "[update — to refresh existing file]"
---

# /contribute

Generate or update CONTRIBUTING.md by analyzing your codebase's conventions, build system, test patterns, and commit style.

## Input

<contribute_input> #$ARGUMENTS </contribute_input>

## Process

### Step 1: Analyze Codebase

Read these sources **in parallel** to detect project conventions:

| Source | What to Extract |
|--------|----------------|
| `CLAUDE.md` / `AGENT_RULES.md` | Coding standards, mandatory checks |
| `Package.swift` / `package.json` / `Cargo.toml` / `pyproject.toml` / `go.mod` / `Gemfile` | Language, version, dependencies, build commands |
| `scripts/` directory | Build scripts, test scripts, verify scripts |
| `.gitignore` | Tooling and artifact patterns |
| `git log --oneline -20` | Commit message conventions |
| `Tests/` or `test/` or `spec/` | Test framework, patterns |
| `README.md` | Architecture section, install instructions |
| `.github/workflows/` | CI configuration |
| `.editorconfig` / `.prettierrc` / `.eslintrc` / `.swiftlint.yml` | Code style tools |

### Step 2: Detect Conventions

Determine these automatically from the analysis:

```yaml
language: ""              # Swift, TypeScript, Rust, Python, Ruby, Go
language_version: ""      # e.g., 5.9, 18.x, 1.75
build_command: ""         # swift build, npm run build, cargo build
test_command: ""          # swift test, npm test, pytest, go test ./...
verify_command: ""        # ./scripts/verify_release.sh (if exists)
commit_style: ""          # conventional (feat/fix/docs) or freeform
branch_strategy: ""       # feature-branch, trunk-based
linter: ""                # none, swiftlint, eslint, rubocop, clippy, ruff
formatter: ""             # none, prettier, rustfmt, black, swiftformat
ci_provider: ""           # none, github-actions, gitlab-ci, circleci
prerequisites: []         # Xcode CLT, Node 18+, Rust toolchain, etc.
```

Present the detected conventions to the user: "I detected [language] with [build/test commands] and [commit style]. Anything to adjust?"

### Step 3: Generate CONTRIBUTING.md

If the argument is `update` and `CONTRIBUTING.md` exists, read the existing file and update sections that are out of date. Otherwise, generate fresh.

Write the file:

```markdown
# Contributing to [Product]

Thanks for your interest in contributing! This guide will help you get started.

## Development Setup

### Prerequisites

- [Language] [version]+
- [Other tools detected from analysis]

### Clone and Build

\`\`\`bash
git clone https://github.com/OWNER/REPO.git
cd REPO
[build_command]
\`\`\`

### Run Tests

\`\`\`bash
[test_command]
[verify_command — if exists]
\`\`\`

## Making Changes

### 1. Create a Branch

\`\`\`bash
git checkout main && git pull
git checkout -b feat/your-feature    # or: fix/bug-description
\`\`\`

### 2. Code Style

[If linter detected:]
We use [linter]. Run before committing:
\`\`\`bash
[lint command]
\`\`\`

[If no linter:]
Follow the patterns in the code you're changing. Match naming conventions, indentation, and structure.

### 3. Commit Messages

[If conventional commits detected:]
We use [conventional commits](https://www.conventionalcommits.org/):
\`\`\`
feat(scope): add new feature
fix(scope): resolve bug
docs: update documentation
chore: maintenance task
\`\`\`

[If freeform:]
Write clear, descriptive commit messages. Start with a verb: "Add", "Fix", "Update", "Remove".

### 4. Tests

[If test framework detected:]
- Add tests for new functionality
- Ensure all existing tests pass
- Run: `[test_command]`

### 5. Submit a Pull Request

\`\`\`bash
git push origin feat/your-feature
gh pr create --title "feat: Description" --body "Closes #N"
\`\`\`

**PR checklist:**
- [ ] Tests pass: \`[test_command]\`
[If verify exists:]
- [ ] Verification passes: \`[verify_command]\`
- [ ] Commit messages follow conventions
- [ ] One logical change per PR

## Project Architecture

[If README has architecture section, extract and include it.]
[Otherwise, generate a source tree from `find . -name "*.swift" -o -name "*.ts" | head -20`]

## What We're Looking For

- Bug fixes with test cases
- Documentation improvements
- Performance improvements with benchmarks
- New features (please open an issue first to discuss)

## What We're NOT Looking For

- Style-only changes without functional improvement
- New dependencies without prior discussion
- Features outside the project's scope

## Need Help?

- Open an [issue](https://github.com/OWNER/REPO/issues)
- Read the [README](README.md) for architecture overview
[If discussions enabled:]
- Start a [discussion](https://github.com/OWNER/REPO/discussions)
```

### Step 4: Validate

Before writing, verify:
- All commands mentioned in the doc actually work when run
- File paths referenced exist in the project
- The architecture section matches current source tree

### Step 5: Write and Offer Commit

Write `CONTRIBUTING.md` to the project root.

```
✓ CONTRIBUTING.md generated

  Detected:
    Language:     [language] [version]
    Build:        [build_command]
    Test:         [test_command]
    Commits:      [conventional or freeform]
    Linter:       [name or none]
    CI:           [provider or none]

  Written: CONTRIBUTING.md ([N] lines)

  Commit with: git add CONTRIBUTING.md && git commit -m "docs: add CONTRIBUTING.md"
```
