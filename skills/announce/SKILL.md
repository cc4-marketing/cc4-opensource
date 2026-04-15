---
name: announce
description: "Generate release announcements for social media from your CHANGELOG."
argument-hint: "[version or platform: 1.4.0, x, linkedin, hn]"
---

# /announce

Generate release announcements for Twitter/X, LinkedIn, Hacker News, and newsletters from your CHANGELOG.

## Input

<announce_input> #$ARGUMENTS </announce_input>

## Process

### Step 1: Extract Release Content

If a version is specified, find that section. Otherwise use the latest:

```bash
# Latest version from CHANGELOG
VERSION=$(grep -oP '(?<=## v)\d+\.\d+\.\d+' CHANGELOG.md | head -1)
```

Read CHANGELOG.md and extract the section for the target version. Also read:
- `README.md` — product name, tagline
- `package.json` / `Package.swift` / `Cargo.toml` — homepage, repo URL
- `index.html` — landing page URL (from canonical link)

Assemble:
```yaml
product_name: ""           # From README title
version: ""                # From CHANGELOG
date: ""                   # From CHANGELOG
features: []               # New features section
fixes: []                  # Bug fixes section
download_url: ""           # GitHub release URL
landing_url: ""            # Landing page URL
repo_url: ""               # GitHub repo URL
```

### Step 2: Generate Announcements

If a specific platform was requested (`x`, `linkedin`, `hn`), generate only that one. Otherwise generate all.

#### Twitter/X (under 280 characters)

```
🚀 [Product] v[VERSION]

[Top feature — one sentence, max 80 chars]

• [Feature 1 — max 40 chars]
• [Feature 2 — max 40 chars]
• [Feature 3 — max 40 chars]

↓ [download_url]

#opensource #[language] #[category]
```

**Rules:**
- Total must be under 280 characters (count carefully)
- Max 3 bullet points — pick the most impressive
- URL at the end
- 2-3 hashtags, lowercase
- No corporate words ("leverage", "streamline", "comprehensive")

#### LinkedIn (300-500 words)

```
🚀 [Product] v[VERSION] is out!

[2-3 sentences: what's new and why it matters. Be specific, name the features.]

What's new:

✅ [Feature 1] — [one-sentence description with specific details]
✅ [Feature 2] — [one-sentence description with specific details]
✅ [Feature 3] — [one-sentence description with specific details]

[One sentence about the project: open source, license, no tracking, etc.]

[One sentence call-to-action: try it, star it, contribute]

Download: [download_url]
Source: [repo_url]
Website: [landing_url]

#OpenSource #[Language] #[Category] #DevTools
```

#### Hacker News (Show HN format)

```
Title: Show HN: [Product] v[VERSION] — [tagline, under 80 chars]

Body:
[Product] is [one sentence: what it is].

What's new in v[VERSION]:

- [Feature 1 with technical detail]
- [Feature 2 with technical detail]
- [Feature 3 with technical detail]

[1-2 sentences: interesting technical decisions or challenges]

[Tech stack: language, frameworks, dependencies]

[Landing page URL]
[GitHub URL]
```

#### GitHub Discussion / Release Notes

```markdown
## What's New in v[VERSION]

[Full feature descriptions from CHANGELOG]

### Highlights

- **[Feature 1]**: [2-3 sentences with context]
- **[Feature 2]**: [2-3 sentences with context]

### Getting Started

[Copy install instructions from README]

### Full Changelog

See [CHANGELOG.md]([repo_url]/blob/main/CHANGELOG.md)

---

If you find [Product] useful, consider [starring the repo]([repo_url]) ⭐
```

#### Newsletter / Email

```
Subject: [Product] v[VERSION] — [Top Feature Name]

Hi,

[Product] v[VERSION] is now available.

What's new:
- [Feature 1]
- [Feature 2]
- [Feature 3]

Download: [URL]

—
[Author]
```

### Step 3: Present Output

Show each announcement with:
- Platform label and character/word count
- The formatted text ready to copy
- For Twitter: verify under 280 chars, warn if over

### Step 4: Copy to Clipboard

Ask which platform to copy first. Use pbcopy (macOS) or xclip (Linux):

```bash
echo "[announcement text]" | pbcopy
```

### Step 5: Posting Schedule

```
Recommended order:
1. GitHub Release notes     — already done via /release
2. Twitter/X                — immediate (catch the momentum)
3. LinkedIn                 — same day (professional audience)
4. Hacker News              — next weekday morning, 8-10am ET
5. Reddit r/[relevant]      — same day as HN
6. Newsletter               — within 48 hours
```

## Voice Guidelines

- **Terse** — say it in fewer words, then cut more
- **Technical** — assume the reader writes code
- **Confident** — state what it does, not what it "helps you" do
- **Honest** — no superlatives unless earned ("fast" only if measurably fast)

**Words to avoid:** powerful, seamless, leverage, utilize, robust, cutting-edge, revolutionary, streamline, comprehensive, next-generation

## Success Output

```
✓ Announcements generated for v{VERSION}

  Twitter/X:      {N} chars {✓ under 280 | ⚠ over 280}
  LinkedIn:       {N} words
  Hacker News:    ready
  GH Discussion:  ready
  Newsletter:     ready

  [Copied Twitter/X to clipboard]

  Posting schedule: GH → Twitter → LinkedIn → HN (tomorrow 9am ET)
```
