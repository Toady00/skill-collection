# Skill Collection

This repository is a personal collection of agent skills. Each skill lives under
`skills/<name>/` and contains a `SKILL.md` file with the instructions and
frontmatter needed by compatible coding agents.

## Installation

The intended installation method is the Vercel Labs `skills` CLI:

```bash
npx skills add Toady00/skill-collection
```

To see the skills available in this repository without installing them:

```bash
npx skills add Toady00/skill-collection --list
```

To install a specific skill:

```bash
npx skills add Toady00/skill-collection --skill beads-common-changelog-release
```

To install every skill from this repository:

```bash
npx skills add Toady00/skill-collection --skill '*'
```

To install globally instead of into the current project:

```bash
npx skills add Toady00/skill-collection --skill '*' --global
```

To target a specific agent, pass `--agent`:

```bash
npx skills add Toady00/skill-collection --skill '*' --agent opencode
```

Agent-specific installs:

```bash
# OpenCode
npx skills add Toady00/skill-collection --skill '*' --agent opencode

# Pi
npx skills add Toady00/skill-collection --skill '*' --agent pi

# Codex
npx skills add Toady00/skill-collection --skill '*' --agent codex

# Claude Code
npx skills add Toady00/skill-collection --skill '*' --agent claude-code
```

The CLI also accepts full Git URLs, including GitLab URLs, if you prefer to use a
specific remote:

```bash
npx skills add git@gitlab.brandondennis.me:toady00/skills.git --skill '*'
```

## Skills

- `beads-common-changelog-release`: Manage beads-backed Common Changelog entries
  and release metadata.
- `commit-message-writer`: Write Git commit messages following the Chris Beams
  seven-rule standard.
- `omg-commands`: Beads CLI command reference for creating and managing issues.
- `omg-epics`: Epic decomposition, dependency wiring, and DAG validation for
  beads.
- `test-writing`: Language and framework specific testing guidance.

## Updating

Use the same CLI to update installed skills later:

```bash
npx skills update
```

## License

MIT
