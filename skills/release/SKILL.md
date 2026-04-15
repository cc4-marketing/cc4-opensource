---
name: release
description: "Automate the full release pipeline: version bump, build, sign, tag, publish, deploy."
argument-hint: "[version: 1.5.0, patch, minor, or major]"
---

# /release

Automate the full release pipeline for open source projects.

## Version

<version_input> #$ARGUMENTS </version_input>

## Process

### Step 1: Determine Version

If no version argument, detect current and ask:

```bash
CURRENT=$(grep -oP '(?<=## v)\d+\.\d+\.\d+' CHANGELOG.md | head -1)
echo "Current version: $CURRENT"
```

If argument is `patch`, `minor`, or `major`, calculate the bump automatically:
- `patch`: X.Y.Z → X.Y.Z+1
- `minor`: X.Y.Z → X.Y+1.0
- `major`: X.Y.Z → X+1.0.0

If a specific version like `1.5.0` is given, use it directly.

If no argument and no CHANGELOG.md, **ask the user**: "What version are you releasing?"

### Step 2: Pre-flight Checks

Run ALL of these. Abort if any fail:

```bash
# Clean working tree
git diff --exit-code && git diff --cached --exit-code

# On main/master branch
BRANCH=$(git branch --show-current)
[[ "$BRANCH" == "main" || "$BRANCH" == "master" ]]

# Tests pass — detect project type and run appropriate command
if [ -f "scripts/verify_release.sh" ]; then
    ./scripts/verify_release.sh
elif [ -f "Package.swift" ]; then
    swift test
elif [ -f "package.json" ]; then
    npm test
elif [ -f "Cargo.toml" ]; then
    cargo test
elif [ -f "Gemfile" ]; then
    bundle exec rake test
elif [ -f "pyproject.toml" ] || [ -f "setup.py" ]; then
    pytest
elif [ -f "go.mod" ]; then
    go test ./...
fi
```

If pre-flight fails, tell the user exactly what failed and stop.

### Step 3: Version Bump

Find ALL files containing the old version and update them:

```bash
OLD_VERSION="$CURRENT"
NEW_VERSION="$TARGET"
grep -rl "$OLD_VERSION" --include="*.swift" --include="*.sh" --include="*.md" \
    --include="*.html" --include="*.xml" --include="*.json" --include="*.toml" \
    --include="*.py" --include="*.rb" --include="*.yaml" --include="*.yml" .
```

Update each file. Common locations:
- Build scripts (version variables, Info.plist templates)
- README.md (version badge/string)
- package.json / Cargo.toml / pyproject.toml (version field)
- Landing page (version badge, download links)
- Update checker / appcast (if applicable)

**Increment build number** if the project uses one (e.g., CFBundleVersion for macOS).

Show the user what was changed and ask for confirmation before proceeding.

### Step 4: Update Changelog

Check if CHANGELOG.md has a section for the new version. If not, generate one:

```bash
LAST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "")
if [ -n "$LAST_TAG" ]; then
    git log ${LAST_TAG}..HEAD --oneline --no-merges
fi
```

Create a new section at the top of CHANGELOG.md:

```markdown
## vX.Y.Z — YYYY-MM-DD

### New features
[Extracted from feat: commits]

### Bug fixes
[Extracted from fix: commits]

### Changes
[Everything else]
```

### Step 5: Commit Version Bump

```bash
git add -A
git commit -m "release: vX.Y.Z — [summary from changelog]"
```

### Step 6: Build Release Artifact

Detect the project type and build:

```bash
if [ -f "scripts/build_dmg.sh" ]; then
    ./scripts/build_dmg.sh                  # macOS DMG
elif [ -f "Package.swift" ]; then
    swift build -c release                  # Swift
elif [ -f "package.json" ]; then
    npm run build                           # Node.js
elif [ -f "Cargo.toml" ]; then
    cargo build --release                   # Rust
elif [ -f "Gemfile" ]; then
    gem build *.gemspec                     # Ruby gem
elif [ -f "pyproject.toml" ]; then
    python -m build                         # Python
elif [ -f "go.mod" ]; then
    go build -o dist/ ./...                 # Go
fi
```

If no build script is detected, ask the user for the build command.

### Step 7: Sign (if applicable)

Check for Sparkle EdDSA signing:

```bash
SIGN_TOOL=$(find .build -name "sign_update" -type f 2>/dev/null | head -1)
if [ -n "$SIGN_TOOL" ]; then
    ARTIFACT=$(ls *.dmg 2>/dev/null | head -1)
    if [ -n "$ARTIFACT" ]; then
        SIG=$("$SIGN_TOOL" "$ARTIFACT")
        echo "$SIG"
        # Parse signature and length, update appcast.xml
    fi
fi
```

If appcast.xml exists, add a new `<item>` with the signature. Amend the release commit.

### Step 8: Tag and Push

```bash
git tag "v${NEW_VERSION}"
git push origin $(git branch --show-current) --tags
```

### Step 9: Create GitHub Release

```bash
# Find the artifact
ARTIFACT=$(ls *.dmg *.tar.gz *.zip dist/*.whl 2>/dev/null | head -1)

# Extract release notes from CHANGELOG
# (everything between the new version header and the previous version header)

gh release create "v${NEW_VERSION}" \
    ${ARTIFACT:+$ARTIFACT} \
    --title "Product v${NEW_VERSION} — Summary" \
    --notes-file /tmp/release-notes.md
```

### Step 10: Deploy Landing Page (if applicable)

```bash
if [ -f "index.html" ]; then
    # Check for Cloudflare Pages
    if command -v wrangler &>/dev/null || npx wrangler --version &>/dev/null 2>&1; then
        mkdir -p /tmp/deploy
        cp index.html /tmp/deploy/
        [ -f og-image.png ] && cp og-image.png /tmp/deploy/
        npx wrangler pages deploy /tmp/deploy --project-name=PROJECT --branch=main --commit-dirty=true
    fi
fi
```

If no deployment config is detected, skip and tell the user.

### Step 11: Close Milestone (if applicable)

```bash
MILESTONE=$(gh api repos/{owner}/{repo}/milestones \
    --jq ".[] | select(.title | contains(\"${NEW_VERSION}\")) | .number" 2>/dev/null)
if [ -n "$MILESTONE" ]; then
    gh api repos/{owner}/{repo}/milestones/$MILESTONE --method PATCH -f state="closed"
fi
```

### Step 12: Summary

```
✓ Release v{VERSION} complete

  Version bump:   N files updated
  Changelog:      CHANGELOG.md updated
  Build:          {artifact name} ({size})
  Signed:         {EdDSA ✓ or N/A}
  Tag:            v{VERSION} pushed
  Release:        {URL}
  Landing page:   {deployed or skipped}
  Milestone:      {closed or N/A}

  Next: run /announce to generate social posts
```
