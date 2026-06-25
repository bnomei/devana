# devana

[![skills.sh](https://skills.sh/b/bnomei/devana)](https://skills.sh/bnomei/devana)
[![GitHub Release](https://img.shields.io/github/v/release/bnomei/devana?label=release)](https://github.com/bnomei/devana/releases)
[![GitHub Downloads](https://img.shields.io/github/downloads/bnomei/devana/total?label=downloads)](https://github.com/bnomei/devana/releases)
[![License](https://img.shields.io/github/license/bnomei/devana)](https://github.com/bnomei/devana/blob/main/LICENSE)
[![Discord](https://flat.badgen.net/badge/discord/bnomei?color=7289da&icon=discord&label)](https://discordapp.com/users/bnomei)
[![Buymecoffee](https://flat.badgen.net/badge/icon/donate?icon=buymeacoffee&color=FF813F&label)](https://www.buymeacoffee.com/bnomei)

Use `devana-bug-hunt` as a semantic bug hunter, not as a normal code review.

Keep devana focused on source-visible runtime bugs only: no tests, builds, installs, services, or network. Reports are written under `.devana/`; the first 3 lines and last 2 lines are deterministic so agents can scan reports with `head` and `tail`.

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

## Arrows and Trails

Trails are devana's hunt lenses. Pass them as slash-command arguments to focus the hunt, or use `--all` when you want every lens worked before devana stops.

```text
/devana-bug-hunt                              # all trails; stop when strong candidates are exhausted
/devana-bug-hunt inside-out-paths             # one trail
/devana-bug-hunt boundaries-oracles            # one trail
/devana-bug-hunt inside-out-paths dataflow-boundaries   # multiple trails
/devana-bug-hunt --all                        # all trails; do not stop until each trail has been actively hunted
```

Trail slugs (the argument tokens):

- `inside-out-paths`
- `outside-in-entrypoints`
- `invariants-contracts`
- `boundaries-oracles`
- `dataflow-boundaries`
- `state-lifecycle`
- `contracts-errors`
- `cache-persistence`
- `security-boundaries`

`--all` is stricter than a bare `/devana-bug-hunt`. It means exhaust every trail above — map the repo through each lens, send Arrows per trail when subagents are available, and only finish when all nine have been covered. Do not bail early because one trail looks quiet.

You can still steer scope in the prompt (topic, path, subsystem). Trail args choose the lens; free text narrows where to point it.

```text
Use /devana-bug-hunt on this project. Focus auth and ownership.
```

```text
Use /devana-bug-hunt outside-in-entrypoints on this project.
```

```text
Use /devana-bug-hunt cache-persistence on src/api.
```

When subagents are available, devana can send Arrows: narrow scouts that inspect one trail or scope, return candidates, and leave validation, deduplication, and report writing to lead devana.

What each trail hunts:

- `inside-out-paths`: core functions outward to reachable bad states.
- `outside-in-entrypoints`: routes, CLI commands, jobs, webhooks, UI actions, public APIs, or SDK methods inward.
- `invariants-contracts`: docs, schemas, callers, callees, units, optionality, ownership, errors, idempotency, and returns.
- `boundaries-oracles`: empty, zero, min/max, duplicate, reordered, missing, null, expired, cancelled, retried, and just-outside values.
- `dataflow-boundaries`: input or state through transforms to sinks.
- `state-lifecycle`: initialization, transitions, cleanup, retries, cancellation, subscriptions, timers, workers, sessions, locks, and disposal.
- `contracts-errors`: adapters, implementations, request/response shapes, return values, errors, nullability, timeouts, and partial failure.
- `cache-persistence`: cache keys, canonicalization, invalidation, transactions, stale reads, and background jobs.
- `security-boundaries`: auth, ownership, tenant/project/user scoping, paths, injection, secrets, deserialization, and unsafe policy fallbacks.

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
