# Devana

[![skills.sh](https://skills.sh/b/bnomei/devana)](https://skills.sh/bnomei/devana)
[![GitHub Release](https://img.shields.io/github/v/release/bnomei/devana?label=release)](https://github.com/bnomei/devana/releases)
[![GitHub Downloads](https://img.shields.io/github/downloads/bnomei/devana/total?label=downloads)](https://github.com/bnomei/devana/releases)
[![License](https://img.shields.io/github/license/bnomei/devana)](https://github.com/bnomei/devana/blob/main/LICENSE)
[![Discord](https://flat.badgen.net/badge/discord/bnomei?color=7289da&icon=discord&label)](https://discordapp.com/users/bnomei)
[![Buymecoffee](https://flat.badgen.net/badge/icon/donate?icon=buymeacoffee&color=FF813F&label)](https://www.buymeacoffee.com/bnomei)

Use `devana-bug-hunt` as a semantic bug hunter, not as a normal code review.

Keep Devana focused on source-visible runtime bugs only: no tests, builds, installs, services, or network. Reports are written under `.devana/`; the first 3 lines and last 2 lines are deterministic so agents can scan reports with `head` and `tail`.

## Install

Install from GitHub:

[Release ZIP](https://github.com/bnomei/devana/releases/latest/download/devana.zip)

Or via Skills.sh:

```bash
npx skills add bnomei/devana --skill devana-bug-hunt
```

## Hunt Yourself

Run a one-shot hunt when you want the current project inspected now.

Codex:

```text
Use $devana-bug-hunt on this project.
```

Claude Code:

```text
Use /devana-bug-hunt on this project.
```

## `/goal` use Devana

Use a goal when you want the session to keep working until Devana has exhausted strong candidates.

[Codex goals](https://developers.openai.com/codex/use-cases/follow-goals):

```text
/goal Use $devana-bug-hunt on this project.
```

 [Claude Code `/goal`](https://code.claude.com/docs/en/goal):

```text
/goal Use /devana-bug-hunt on this project.
```

## `/loop` | Schedule Devana

Use `/loop` for a session-local recurring hunt, or schedule Devana when you want recurring background checks.

[Codex automations](https://developers.openai.com/codex/app/automations) prompt:

```text
Use $devana-bug-hunt on this project. 
```

[Claude Code `/loop`](https://code.claude.com/docs/en/scheduled-tasks):

```text
/loop Use /devana-bug-hunt on this project.
```

[Claude Code Desktop scheduled tasks](https://code.claude.com/docs/en/desktop-scheduled-tasks) prompt:

```text
Use /devana-bug-hunt on this project.
```

## License

MIT
