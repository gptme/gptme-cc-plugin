---
name: context
description: >
  Inject gptme workspace lessons, skills, and memory into this Claude Code
  session. Use when the user invokes /gptme:context or wants to load gptme-style
  persistent memory and behavioral rules into the current session. Do NOT use
  if there is no gptme workspace in the current directory or ~/.config/gptme/.
---

# gptme:context

Load gptme workspace knowledge (lessons, skills, memory) into this Claude Code
session for use in subsequent tasks.

## When to Use

- User invokes `/gptme:context`
- User wants to leverage gptme workspace memory or lessons in this CC session
- Working in a project that has a `gptme.toml` and `lessons/` directory

## Procedure

### From a gptme workspace (recommended)

If the current project has a `lessons/` directory and `gptme.toml`:

```bash
LESSONS_DIR="$(git rev-parse --show-toplevel 2>/dev/null)/lessons"
if [ -d "$LESSONS_DIR" ]; then
  echo "=== gptme Lessons ==="
  find "$LESSONS_DIR" -name "*.md" -not -name "README.md" | \
    head -20 | xargs cat 2>/dev/null | head -3000
else
  echo "No lessons/ directory found in current workspace"
fi
```

### From gptme global config

```bash
GLOBAL_LESSONS="${HOME}/.config/gptme/lessons"
if [ -d "$GLOBAL_LESSONS" ]; then
  echo "=== Global gptme Lessons ==="
  find "$GLOBAL_LESSONS" -name "*.md" | head -10 | xargs cat | head -2000
fi
```

### List available skills

```bash
# List gptme skills if gptme-util is available
if command -v gptme-util &>/dev/null; then
  gptme-util skills list 2>/dev/null
fi

# Or list directly
SKILLS_DIR="$(git rev-parse --show-toplevel 2>/dev/null)/skills"
[ -d "$SKILLS_DIR" ] && find "$SKILLS_DIR" -name "SKILL.md" | head -20 | while read f; do
  echo "$(basename $(dirname $f)): $(head -5 $f | grep description | cut -d: -f2-)"
done
```

## After loading context

Once injected, refer to the loaded lessons and skills naturally in subsequent
prompts. Example: "Now apply the lessons you just loaded to review this code."

## Notes

- Context is injected into this session only — it does not persist after the session ends
- For persistent behavior, install a gptme workspace with the relevant `lessons/` and `skills/` directories
- The Bob agent (`https://github.com/ErikBjare/bob`) is an example of a rich gptme workspace

## Related

- [gptme docs](https://gptme.org)
- [gptme agent template](https://github.com/gptme/gptme-agent-template) — fork to create your own workspace
- `/gptme:run` — delegate tasks to gptme
- `/gptme:review` — code review via gptme
