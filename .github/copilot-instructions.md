# Copilot Instructions

## What this repo is

A collection of **Claude Code skills and plugins**. Skills are instruction files (SKILL.md) that teach Claude Code how to perform structured workflows. Plugins group related skills and are distributed via the Claude Code plugin marketplace.

## Repository structure

```
.claude-plugin/marketplace.json          # Top-level marketplace registry
plugins/<plugin-name>/
  .claude-plugin/plugin.json             # Plugin metadata (name, version)
  README.md
  skills/<skill-name>/
    SKILL.md                             # The skill itself (YAML frontmatter + Markdown)
    evals/evals.json                     # Evaluation test cases for the skill
```

## Key conventions

### SKILL.md frontmatter

Every SKILL.md starts with a YAML frontmatter block that controls when Claude Code auto-triggers the skill:

```yaml
---
name: skill-name
description: |
  Prose description used by Claude to decide when to invoke this skill.
  Include explicit trigger phrases (in both English and Russian if needed).
  Include "Examples:" section with user → action pairs.
---
```

The `description` field is the triggering signal — write it to match the natural-language phrases users actually type, including non-English triggers where applicable.

### Plugin metadata files

- `marketplace.json` — top-level registry; lists all plugins with `name`, `source` path, `version`, `description`. Update when adding or renaming plugins.
- `plugin.json` — per-plugin metadata; mirrors key fields from `marketplace.json`. Keep versions in sync between these two files.

### Evals

`evals/evals.json` contains an array of `{id, name, prompt, expected_output, files}` objects. Add an eval for every distinct trigger scenario the skill handles. `expected_output` is a prose description of what the skill should do — not a literal string match.

### Language conventions

- Skill/plugin names and file names: English, kebab-case
- `describe-mr` skill output: **Russian** (including all section headers). Technical terms (API, endpoint, middleware) stay in English.
- `fix-bug` skill output: English, with Russian phrases accepted as input triggers.

### Plugin `strict: true`

The `dev-pro` plugin is marked `"strict": true` in `marketplace.json`. This means Claude should follow the skill workflow exactly, not improvise.

## Installation command (for reference)

```bash
/plugins add https://github.com/ar2r/claude-skills
/plugins update dev-pro
```

## External dependencies

- `glab` — required by `describe-mr` to publish descriptions to GitLab (`glab mr update`). Must be installed and authenticated.
