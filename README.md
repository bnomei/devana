# Devana

[![skills.sh](https://skills.sh/b/bnomei/devana)](https://skills.sh/bnomei/devana)
[![GitHub Release](https://img.shields.io/github/v/release/bnomei/devana?label=release)](https://github.com/bnomei/devana/releases)
[![GitHub Downloads](https://img.shields.io/github/downloads/bnomei/devana/total?label=downloads)](https://github.com/bnomei/devana/releases)
[![License](https://img.shields.io/github/license/bnomei/devana)](https://github.com/bnomei/devana/blob/main/LICENSE)
[![Discord](https://flat.badgen.net/badge/discord/bnomei?color=7289da&icon=discord&label)](https://discordapp.com/users/bnomei)
[![Buymecoffee](https://flat.badgen.net/badge/icon/donate?icon=buymeacoffee&color=FF813F&label)](https://www.buymeacoffee.com/bnomei)

Devana is an agent skill for static semantic bug hunts. Use `devana-bug-hunt`
when you want an agent to inspect source code for reachable runtime defects, not
when you want a normal code review.

Devana focuses on contracts, invariants, authority boundaries, state changes,
dataflow, persistence, and failure paths. It rejects style feedback, lint output,
architecture opinions, missing-test comments, and generic cleanup suggestions.

## Install

Install the latest release ZIP:

[Download `devana.zip`](https://github.com/bnomei/devana/releases/latest/download/devana.zip)

Or install with Skills.sh:

```bash
npx skills add bnomei/devana --skill devana-bug-hunt
```

## Quickstart

Use this when you want the current repository inspected now.

### Prerequisites

- Devana is installed in your agent host.
- You are in the repository you want to inspect.
- The agent can read the repository source files.

### Run a one-shot hunt

In Codex:

```text
Use $devana-bug-hunt on this project.
```

In Claude Code:

```text
Use /devana-bug-hunt on this project.
```

Expected result:

```text
reports written: 0
stop reason: no actionable findings
```

If Devana finds a reportable bug, it writes one or more Markdown reports under
`.devana/` and prints the filenames before it stops.

Every finished hunt reports the number of reports written, any report filenames,
and the stop reason.

## What Devana reports

Devana writes a report only when it can show all of these:

- a specific affected location
- a realistic runtime path
- a violated invariant, contract, state transition, trust boundary, or failure
  path expectation
- a concrete counterexample
- source-backed proof and counterevidence checked
- the strongest false-positive reason checked
- user, data, security, availability, or correctness impact
- no obvious duplicate

Devana prefers zero reports over noisy reports. It does not run tests, builds,
package installs, migrations, services, or network calls during a hunt.

## Report files

Reports are durable Markdown records under `.devana/`:

```text
.devana/YYYYMMDDTHHMMSSZ-PN-short-slug.md
```

Use UTC when possible. Keep the slug lowercase ASCII, hyphen-separated, and
under eight words.

Each report includes:

- `DEVANA-FINDING`, `DEVANA-STATE`, and `DEVANA-KEY` header lines
- the affected location, priority, confidence, and security marker
- the violated invariant or contract
- the oracle Devana used as source evidence
- a counterexample and proof shape
- counterevidence checked
- a suggested next step
- deterministic trailing `DEVANA-KEY` and `DEVANA-SUMMARY` lines

See the [report template](skills/devana-bug-hunt/references/report-template.md)
for the exact format.

## Work a report

After Devana writes a report, hand that single file to your normal coding agent:

```text
Please work this Devana report: .devana/YYYYMMDDTHHMMSSZ-PN-short-slug.md
Validate it against the current code, fix it if it is still valid, and update the report status.
```

The agent should read the whole report, inspect the current code, and then either
fix the bug or mark the report as `fixed`, `invalid`, `stale`, `duplicate`, or
`wontfix`.

When updating a report:

- preserve line 1 and the original finding body
- update line 2, `DEVANA-STATE: ...`
- update the final `DEVANA-SUMMARY:` status, priority, and confidence prefix
- keep both `DEVANA-KEY` values stable unless the same finding moved
- add dated notes under `## Status Notes`
- mark `fixed` only after the original counterexample is blocked

## Priority reference

Priority means human review urgency.

| Priority | Use it for |
| --- | --- |
| `P0` | Sensitive, private, or catastrophic bugs, including reachable security, privacy, data loss, auth, tenant isolation, injection, RCE, path traversal, or secret exposure issues. |
| `P1` | Likely serious bugs, such as important workflow breakage, persisted inconsistency, high-confidence crashes, or skipped billing, publishing, sync, permission, or cleanup behavior. |
| `P2` | Plausible actionable bugs with concrete proof but uncertain impact. |
| `P3` | Minor or uncertain runtime concerns that are still actionable. Devana should not emit `P3` for vague suspicion. |

## Trail reference

Trails are hunt lenses. Valid command arguments are empty, `--all`, or one or
more trail slugs. Empty invocation means all trails are available, but the hunt
is not exhaustive. `--all` means Devana must actively cover all nine trails
before it stops. Unknown tokens stop the run and list the valid trails.

```text
/devana-bug-hunt
/devana-bug-hunt inside-out-paths
/devana-bug-hunt boundaries-oracles
/devana-bug-hunt inside-out-paths dataflow-boundaries
/devana-bug-hunt --all
```

| Trail | Use it to inspect |
| --- | --- |
| `inside-out-paths` | Core functions, handlers, reducers, parsers, state machines, jobs, and state-changing methods. |
| `outside-in-entrypoints` | Routes, CLI commands, consumers, scheduled jobs, webhooks, UI actions, public APIs, SDK methods, and plugin hooks. |
| `invariants-contracts` | Docs, schemas, tests, callers, and callees for fields, units, optionality, ordering, ownership, errors, idempotency, and returns. |
| `boundaries-oracles` | Empty, zero, one, min/max, duplicate, reordered, missing, null, expired, cancelled, retried, and just-outside values. |
| `dataflow-boundaries` | Input and state as they move through transforms to sinks, including trust labels such as tenant, owner, actor, locale, version, and permission. |
| `state-lifecycle` | Initialization, transitions, cleanup, retries, cancellation, concurrency, subscriptions, timers, workers, sessions, locks, and disposal. |
| `contracts-errors` | Adapters, implementations, request and response shapes, return values, errors, nullability, timeouts, and partial failures. |
| `cache-persistence` | Cache keys, canonicalization, tenant/user/locale/version dimensions, invalidation, transactions, stale reads, and background jobs. |
| `security-boundaries` | Auth, ownership, tenant or project scoping, paths, injection, secrets, deserialization, and unsafe policy fallbacks. |

When subagents are available, Devana may send one Arrow per trail. Arrows return
candidates only and must not write reports; lead Devana validates, deduplicates,
prioritizes, and writes reports.

## Scheduled hunts

Use an automation or loop when you want recurring background checks.

Codex automation prompt:

```text
Use $devana-bug-hunt on this project.
```

Claude Code loop:

```text
/loop Use /devana-bug-hunt on this project.
```

## Source files

- [Skill definition](skills/devana-bug-hunt/SKILL.md)
- [Report template](skills/devana-bug-hunt/references/report-template.md)
- [Trail playbook](skills/devana-bug-hunt/references/trail-playbook.md)
- [OpenAI agent metadata](skills/devana-bug-hunt/agents/openai.yaml)

## License

MIT
