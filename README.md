# gptme-cc-plugin

Claude Code skills for [gptme](https://gptme.org) — use gptme's terminal AI agent directly from your Claude Code sessions.

## What is this?

This package adds three Claude Code skills that let you delegate work to gptme without leaving your CC session:

| Skill | Invocation | What it does |
|-------|-----------|-------------|
| `run` | `/gptme:run "<task>"` | Delegate a task to gptme's agent (shell, files, browser) |
| `review` | `/gptme:review` | Multi-lens code review (correctness, security, test coverage) |
| `context` | `/gptme:context` | Inject gptme workspace lessons/memory into this CC session |

## Prerequisites

- [gptme](https://gptme.org) installed and in PATH:
  ```bash
  pip install gptme
  # or
  pipx install gptme
  ```
- Claude Code with Bash tool permissions enabled

## Install

From inside Claude Code:

```
/plugin marketplace add gptme/gptme-cc-plugin
/plugin install gptme@gptme-cc-plugin
```

or from a shell:

```bash
claude plugin marketplace add gptme/gptme-cc-plugin
claude plugin install gptme@gptme-cc-plugin
```

Then restart Claude Code and invoke `/gptme:run`, `/gptme:review`, or `/gptme:context`.
`claude plugin details gptme` should list three skills: `context`, `review`, `run`.

The skills are namespaced by the plugin name (`gptme`), which is why they are named
`run`, `review` and `context` inside the plugin. Cloning this repo into
`~/.claude/skills/gptme` does **not** work: personal skills are discovered at
`~/.claude/skills/<name>/SKILL.md`, and this repo keeps its skills one level deeper.

To try a local checkout without installing, run `claude --plugin-dir /path/to/gptme-cc-plugin`.

## Usage

### Delegate a task

```
/gptme:run "find all TODO comments in this repo and create GitHub issues for each"
```

### Multi-lens code review

```
/gptme:review
```

Runs gptme with three independent review passes (correctness, security, test coverage) on your current `git diff HEAD`.

### Load workspace context

```
/gptme:context
```

Injects lessons, skills, and memory from the current gptme workspace into this Claude Code session.

## Why?

The pattern was proven by OpenAI's [codex-plugin-cc](https://github.com/openai/codex-plugin-cc): packaging a competing agent as a CC skill reaches CC users with zero friction. A gptme plugin is a distribution play — CC users who try gptme via a skill become gptme users.

## Architecture

These are **skills-based** (SKILL.md) rather than MCP-based. Each skill teaches Claude Code to invoke the local `gptme` binary via the Bash tool. No server required.

Future work: when gptme ships an MCP server mode, a Phase 2 MCP-based version will expose `gptme.run` and `gptme.review` as structured tools.

## Related

- [gptme](https://github.com/gptme/gptme) — the agent these skills delegate to
- [gptme-agent-template](https://github.com/gptme/gptme-agent-template) — fork to create your own gptme workspace
- [Bob](https://github.com/ErikBjare/bob) — an example of a rich gptme workspace (the agent that built this)

## License

MIT
