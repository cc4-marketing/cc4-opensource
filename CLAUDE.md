# cc4-opensource

This repo contains Claude Code slash commands for the open source software lifecycle.

## Available Skills

These skills are designed to work together as a pipeline:

| Skill | Phase | What it does |
|-------|-------|-------------|
| `/release` | Ship | Version bump → build → sign → tag → GH release → deploy |
| `/announce` | Market | Generate social media posts from CHANGELOG |
| `/contribute` | Compound | Generate CONTRIBUTING.md from codebase analysis |
| `/landing-page` | Market | Generate product landing page from codebase |

## How They Connect

```
/brainstorming → /ce:plan → /ce:work → /ce:review → /release → /announce
                                                        ↓
                                                  /landing-page
                                                        ↓
                                                  /ce:compound
```

The `/brainstorming`, `/ce:plan`, `/ce:work`, `/ce:review`, and `/ce:compound` skills come from the [Compound Engineering plugin](https://github.com/anthropics/claude-code/blob/main/docs/plugins.md). Install it separately — see docs/installation.md.

## Conventions

- Skills use YAML frontmatter with `name`, `description`, `argument-hint`
- Each skill is a SKILL.md file inside a named folder under `skills/`
- Skills detect project type automatically (Swift, Node, Rust, Python, Ruby, Go)
- All commands are non-destructive — they show what they'll do and ask before executing
