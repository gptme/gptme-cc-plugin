---
name: review
description: >
  Run a multi-lens code review via gptme on the current git diff or a specific
  file. Use when the user invokes /gptme:review or asks for a gptme-style
  parallel code review. Do NOT use when a simple in-session review suffices.
---

# gptme:review

Delegate code review to gptme, which runs independent correctness, security,
and test-coverage passes on your current changes.

## When to Use

- User invokes `/gptme:review`
- User wants a second opinion or multi-lens review on the current diff
- Current changes are non-trivial and a systematic bug-finding pass adds value

## When Not to Use

- Diff is tiny/mechanical (config change, typo fix)
- User just wants a quick summary, not a structured review
- `gptme` not in PATH

## Procedure

```bash
# Review current staged+unstaged diff
DIFF=$(git diff HEAD)
if [ -z "$DIFF" ]; then
  DIFF=$(git diff --cached)
fi

if [ -z "$DIFF" ]; then
  echo "No changes to review (git diff HEAD is empty)"
  exit 0
fi

gptme --non-interactive \
  "Review the following code changes. Run three passes: (1) correctness — logic bugs, broken invariants; (2) security — unsafe input handling, secret leakage; (3) test coverage — missing regression tests, unverified edge cases. For each finding include: file, line, severity (high/medium/low), and a concrete fix hint. Output findings as a markdown list ordered by severity." \
  - \
  "$DIFF"
```

## Review a specific file

```bash
gptme --non-interactive \
  "Review this file for bugs, security issues, and missing test coverage. Output findings as a markdown list ordered by severity." \
  - \
  "$(cat path/to/file.py)"
```

## Tips

- The `--non-interactive` flag is required for non-blocking execution
- For large diffs, consider reviewing one file at a time
- gptme's review is independent of this Claude Code session — it won't share your prior context

## Related

- [gptme docs](https://gptme.org)
- `/gptme:run` — delegate arbitrary tasks to gptme
- `/gptme:context` — inject gptme lessons into this session
