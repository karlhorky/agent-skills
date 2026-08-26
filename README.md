# Agent Skills

> Agent Skills for AI agents

## Install

Install a skill in the current project:

```sh
npx skills add karlhorky/agent-skills --skill <skill-name> --agent universal --yes
```

For example:

```sh
npx skills add karlhorky/agent-skills --skill surgical-commits --agent universal --yes
```

### Global Install

Install a skill globally:

```sh
npx skills add karlhorky/agent-skills --skill <skill-name> --agent universal --global --yes
```

For example:

```sh
npx skills add karlhorky/agent-skills --skill surgical-commits --agent universal --global --yes
```

## Non-Universal Agents

For misbehaving AI agents (eg. [Claude Code](https://x.com/karlhorky/status/2092539937170055548)) which don't follow the standard `.agents/skills` locations, symlink their skills directory to it.

For example, to fix Claude Code:

```bash
ln -s ../.agents/skills .claude/skills
ln -s "$HOME/.agents/skills" "$HOME/.claude/skills"
```
