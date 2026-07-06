---
name: gptme-run
description: >
  Delegate a multi-step task to gptme, a terminal-based AI agent with its own
  tools (shell, files, browser). Use when the user invokes /gptme:run or asks
  to have gptme work on something autonomously using its own context and tools.
  Do NOT use for simple questions that Claude Code can answer directly.
---

# gptme:run

Invoke gptme non-interactively to execute a task using its own tool access.

## When to Use

- User says `/gptme:run "<task>"` or asks to delegate work to gptme
- Task requires gptme's specific tools or workspace context (gptme.toml, lessons)
- User wants gptme's independent perspective on a problem

## When Not to Use

- Simple questions Claude Code can answer directly
- Tasks that require interactive back-and-forth (use `gptme` in a terminal instead)
- When `gptme` is not installed in PATH

## Procedure

First check that gptme is available:

```bash
which gptme || echo "ERROR: gptme not in PATH — install with: pip install gptme"
```

Run the delegated task:

```bash
gptme --non-interactive --output-format json "$TASK"
```

Where `$TASK` is the user's instruction from the `/gptme:run` argument.

### Flags

- `--non-interactive`: Run without interactive prompts; emit output and exit
- `--output-format json`: Emit structured JSON per line for clean parsing
- Omit `--no-workspace` to let gptme load its own context from `gptme.toml` if present in the current directory

### Parsing output

The JSON stream contains message objects. The final assistant message is the result:

```bash
gptme --non-interactive --output-format json "summarize the project" | \
  python3 -c "import json,sys; msgs=[json.loads(l) for l in sys.stdin if l.strip()]; print([m for m in msgs if m.get('role')=='assistant'][-1]['content'])"
```

For most uses, present the raw gptme output directly — it's already human-readable.

## Example

User: `/gptme:run "find all TODO comments in this repo and summarize them"`

```bash
gptme --non-interactive "find all TODO comments in this repo and summarize them"
```

## Troubleshooting

- **`gptme: command not found`**: Install with `pip install gptme` or `pipx install gptme`
- **Hangs**: gptme may be waiting for a tool confirmation — use `--non-interactive` flag
- **No output**: Check `--output-format json` or omit it for human-readable output

## Related

- [gptme docs](https://gptme.org)
- `/gptme:review` — multi-lens code review via gptme
- `/gptme:context` — inject gptme workspace lessons/memory into this session
