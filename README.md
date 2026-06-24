# Devana

[![skills.sh](https://skills.sh/b/bnomei/devana)](https://skills.sh/bnomei/devana)

Use `devana-bug-hunt` mainly as a scheduled semantic bug hunter, not as a normal code review.

- Codex: create a project automation that runs `Use $devana-bug-hunt on this project...`.
  Docs: https://developers.openai.com/codex/app/automations
- Claude Code: use `/loop` with `Use /devana-bug-hunt on this project...` for a session-local loop.
  Docs: https://code.claude.com/docs/en/scheduled-tasks

Keep the prompt focused: source-visible runtime bugs only, no tests/builds/installs/services/network. Devana writes reports directly under `.devana/`; the first 3 lines and last 2 lines are deterministic so agents can scan reports with `head` and `tail`.

## Install

Install from GitHub:

```bash
npx skills add bnomei/devana --skill devana-bug-hunt
```

## 

