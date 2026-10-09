# agent-skills

A collection of reusable skills for AI coding agents (Bob, Claude Code, Cursor, etc.).

## Skills

| Skill | Description |
|-------|-------------|
| [`commit-helper`](skills/commit-helper/) | Generate commit messages from staged changes with gitmoji + conventional commits |
| [`pr-helper`](skills/pr-helper/) | Create pull requests with smart auto-generated titles and descriptions |

## Installation

Install a single skill globally using the [skills CLI](https://skills.sh):

```bash
# commit-helper
npx skills@latest add \
  https://github.com/Yashrajsingh2001/agent-skills/tree/main/skills/commit-helper \
  -g -a bob

# pr-helper
npx skills@latest add \
  https://github.com/Yashrajsingh2001/agent-skills/tree/main/skills/pr-helper \
  -g -a bob
```

Or install the whole collection:

```bash
npx skills@latest add Yashrajsingh2001/agent-skills -g -a bob
```

## Verify installation

Open your agent and run `/list-skills` — both skills should appear.

## Skill structure

Each skill is a directory containing a `SKILL.md` with YAML frontmatter:

```
skills/
  commit-helper/
    SKILL.md
  pr-helper/
    SKILL.md
```

## License

MIT
