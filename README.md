# devana

[![skills.sh](https://skills.sh/b/bnomei/devana)](https://skills.sh/bnomei/devana)
[![GitHub Release](https://img.shields.io/github/v/release/bnomei/devana?label=release)](https://github.com/bnomei/devana/releases)
[![GitHub Downloads](https://img.shields.io/github/downloads/bnomei/devana/total?label=downloads)](https://github.com/bnomei/devana/releases)
[![License](https://img.shields.io/github/license/bnomei/devana)](https://github.com/bnomei/devana/blob/main/LICENSE)
[![Discord](https://flat.badgen.net/badge/discord/bnomei?color=7289da&icon=discord&label)](https://discordapp.com/users/bnomei)
[![Buymecoffee](https://flat.badgen.net/badge/icon/donate?icon=buymeacoffee&color=FF813F&label)](https://www.buymeacoffee.com/bnomei)

Use `devana-bug-hunt` as a semantic bug hunter, not as a normal code review. It reads source code for reachable runtime defects in contracts, invariants, boundaries, state, dataflow, persistence, and failure paths.

Devana is useful when you want low-noise findings that are backed by a concrete counterexample, not style feedback or generic quality advice. It can run as a one-shot hunt, a goal-driven session, or a scheduled loop, and it stays static-only: no tests, builds, installs, services, or network.

```text
┌─────────────────────────────────────────────┐
│ find candidates                             │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│ evaluate invariant, oracle, proof, evidence │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────┐
│ reachable, actionable, non-duplicate?       │
└──────────────┬────────────────────┬─────────┘
               │ no                 │ yes
               ▼                    ▼
┌────────────────────────────┐  ┌───────────────────────────────┐
│ discard lead, keep hunting │  │ write report in .devana/      │
└────────────────────────────┘  └───────────────────────────────┘
```

## What Devana Creates

When Devana finds a reportable bug, it creates an append-only Markdown report under `.devana/`:

```text
.devana/YYYYMMDDTHHMMSSZ-PN-short-slug.md
```

Each report records the affected location, priority, confidence, violated invariant or contract, oracle, counterexample, proof, counterevidence checked, and suggested next step. The first 3 lines and last 2 lines are deterministic so agents can scan reports with `head`, `tail`, or `rg`.

See the [report template](skills/devana-bug-hunt/references/report-template.md) for the exact shape.

## Using Reports

After Devana writes a report, point your normal coding agent at the file and ask it to validate or fix that specific finding:

```text
Please work this Devana report: .devana/YYYYMMDDTHHMMSSZ-PN-short-slug.md
Validate it against the current code, fix it if it is still valid, and update the report status.
```

The agent should read the full report, inspect the current code, then either implement the smallest useful fix or mark the report as `fixed`, `invalid`, `stale`, `duplicate`, or `wontfix`. The deterministic header and trailer make report queues cheap to scan, while the full Markdown body keeps enough proof for a normal agent to pick up the work without needing Devana.

## Install

Install from GitHub:

[Release ZIP](https://github.com/bnomei/devana/releases/latest/download/devana.zip)

Or via Skills.sh:

```bash
npx skills add bnomei/devana --skill devana-bug-hunt
```

## Skill of Hunting Bugs manually

Run a one-shot hunt when you want the current project inspected now.

Codex:

```text
Use $devana-bug-hunt on this project.
```

Claude Code:

```text
Use /devana-bug-hunt on this project.
```

## Arrows and Trails

Trails are hunt lenses. Use no argument for the default all-trail hunt, pass one or more slugs to focus it, or use `--all` to require every trail to be actively covered before devana stops.

```text
/devana-bug-hunt
/devana-bug-hunt inside-out-paths
/devana-bug-hunt boundaries-oracles
/devana-bug-hunt inside-out-paths dataflow-boundaries
/devana-bug-hunt --all
```

Trail slugs:

- `inside-out-paths`
- `outside-in-entrypoints`
- `invariants-contracts`
- `boundaries-oracles`
- `dataflow-boundaries`
- `state-lifecycle`
- `contracts-errors`
- `cache-persistence`
- `security-boundaries`

Free text can narrow the scope:

```text
Use /devana-bug-hunt cache-persistence on src/api.
```

When subagents are available, devana may send one Arrow per trail. Arrows scout narrow scopes and return candidates; lead devana validates, deduplicates, prioritizes, and writes reports.

## `/goal` use devana

Use a goal when you want the session to keep working until devana has exhausted strong candidates.

[Codex goals](https://developers.openai.com/codex/use-cases/follow-goals):

```text
/goal Use $devana-bug-hunt on this project.
```

 [Claude Code `/goal`](https://code.claude.com/docs/en/goal):

```text
/goal Use /devana-bug-hunt on this project.
```

## `/loop` | schedule devana

Use `/loop` for a session-local recurring hunt, or schedule devana when you want recurring background checks.

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
