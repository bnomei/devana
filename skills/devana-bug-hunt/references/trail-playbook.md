# Devana Trail Playbook

Use this only to turn a trail into concrete repo-specific probes. Do not report generic design advice. Every probe must aim at a reachable runtime bug.

## Prompt Construction Rule

Before sending an Arrow, lead Devana must translate the selected trail into a repo-specific prompt:

1. Name exact files, symbols, entry points, data models, state machines, queues, caches, config, or trust boundaries already seen in this repo.
2. Pick only the relevant probes below.
3. Convert those probes into a precise hit list for the Arrow.
4. Include expected proof shape and counterevidence to check.

Do not send an Arrow a bare trail label and expect it to infer this context.

## Postmortem Lens

Use this as a cross-cutting lens when a repo has production workflows, scheduled automation, distributed components, or release/config surfaces. It is not a separate trail; fold the relevant probes into the selected trail prompt.

Look for incident-shaped bug clusters:

- config drift, environment drift, feature-flag rollout mismatch
- stale cache, stale derived state, missing invalidation, cache stampede
- partial failure, retry storm, timeout path, fallback changing semantics
- dependency behavior mismatch, API/schema/version drift
- missing guardrail at deploy, migration, startup, queue, or job boundary
- observability gap that hides success-as-failure or failure-as-success

Prompt use: name the specific config key, flag, dependency, cache, migration, job, or external service in the Arrow prompt. Ask for a concrete event sequence, not a general reliability concern.

Good proof: incident-style timeline showing trigger -> missed guardrail -> wrong runtime state or user-visible impact.

Reject when: the issue is only poor observability or process hygiene without a concrete reachable bad behavior.

Keywords: incident, postmortem, rollout, drift, flag, stale, timeout, retry, fallback, dependency, migration, guardrail, alert.

## inside-out-paths

Start from: core mutators, aggregate roots, reducers, parsers, state machines, transaction coordinators, resource owners, business-rule modules.

Look for:

- required work skipped by branch order, early return, or fallback
- state-changing method reachable with invalid pre-state
- core helper used by callers with incompatible units, ownership, or lifecycle assumptions
- side effects mixed into core logic: storage, network, email, queue, cache, auth

Probes: callers of mutators; public wrappers around core helpers; branches that bypass validation; repeated internal state writes; core functions with external side effects.

Good proof: control-flow trace from core function to caller-visible bad state, with concrete pre-state and caller path.

Reject when: the concern is only large function, low cohesion, abstraction, or refactor preference.

Keywords: mutator, aggregate, reducer, parser, state machine, transaction, business rule, side effect, branch order.

## outside-in-entrypoints

Start from: routes, CLI commands, webhooks, message consumers, scheduled jobs, UI actions, public APIs, SDK methods, plugin hooks.

Look for:

- missing validation at the true boundary
- entry points performing the same action with different auth, ownership, defaults, or side effects
- feature flag, rollout, config, or environment drift changing behavior
- retry, duplicate delivery, timeout, cancellation, or partial failure paths

Probes: external input fields; route/job/webhook handlers; auth middleware ordering; config reads; feature flags; queue consumers; scheduled jobs.

Good proof: entrypoint-to-sink trace with exact input, actor, config, or event order.

Reject when: no external or scheduled path reaches the behavior.

Keywords: route, webhook, consumer, cron, CLI, flag, config, env, retry, duplicate, timeout, cancellation.

## invariants-contracts

Start from: docs, schemas, type names, constructor guards, aggregate rules, state enums, tests, caller/callee expectations.

Look for:

- impossible states that constructors or update paths can still create
- precondition/postcondition mismatch between caller and callee
- aggregate invariant broken across multiple fields, rows, or objects
- schema/runtime mismatch: optional vs required, units, nullability, enum cases, ordering

Probes: unchecked constructors; `validate`, `ensure`, `assert`, `rep_ok`; state enum transitions; schema fields; docstrings; migration defaults.

Good proof: violated invariant plus smallest counterexample value, state, or transition.

Reject when: expected behavior is not encoded in code, docs, tests, schema, or nearby callers.

Keywords: invariant, precondition, postcondition, impossible state, typestate, aggregate, schema, enum, nullability, units.

## boundaries-oracles

Start from: numeric/string/date ranges, collection sizes, parser inputs, pagination, rate limits, time windows, locales, encodings.

Look for:

- off-by-one, empty/null, min/max, just-before/after, duplicate, reordered, expired, cancelled, retried, malformed values
- timezone, locale, Unicode, charset, canonicalization, date parsing, decimal/rounding mismatches
- missing upper bound that can corrupt state or exhaust resources

Probes: comparisons; loops; slicing/indexing; parser branches; limit/page parameters; date/time conversions; canonical signature inputs.

Good proof: concrete boundary value and oracle showing the observed behavior violates source-visible intent.

Reject when: the boundary value is only unusual, not plausibly wrong.

Keywords: empty, null, zero, one, min, max, off-by-one, timezone, locale, encoding, canonicalization, malformed.

## dataflow-boundaries

Start from: user input, auth claims, tenant/project/user IDs, config/env values, serialized fields, cache keys, message payloads.

Look for:

- untrusted input reaching sink without validation or canonicalization
- trust label lost across transforms: tenant, owner, actor, locale, version, permission
- stale or derived value used after source state changed
- producer/consumer field meaning mismatch

Probes: source-to-sink paths; transforms; serializers/deserializers; permission context propagation; joins between actor-owned and resource-owned data.

Good proof: dataflow trace from source to sink with missing/wrong check and affected behavior.

Reject when: the flow is sanitized, reauthorized, canonicalized, or constrained before the sink.

Keywords: source, sink, taint, tenant, owner, actor, permission, transform, serializer, canonicalize, derived value.

## state-lifecycle

Start from: init/use/cleanup, subscriptions, timers, workers, sessions, locks, pooled resources, cancellation, retries.

Look for:

- use before init, mutation after dispose, missing cleanup, duplicate listener/timer/worker
- invalid state transition or missing transition guard
- retry/cancel/timeout leaving partial state
- race, lock order, atomicity, or idempotency violation

Probes: constructors; start/stop hooks; `close`, `dispose`, `finally`, `defer`; lock acquisition order; retry loops; cancellation handlers.

Good proof: state transition trace or event order that reaches invalid state.

Reject when: lifecycle concern cannot affect runtime behavior or resource availability.

Keywords: init, cleanup, dispose, RAII, cancel, timeout, retry, lock, race, atomic, listener, timer, pool.

## contracts-errors

Start from: adapters, interface implementations, API clients, request/response shapes, error paths, fallbacks, retries.

Look for:

- swallowed exception reported as success
- fallback changes semantics or bypasses required side effect
- caller expects throw but callee returns null/empty/partial success
- retry duplicates side effect or ignores idempotency requirement
- API version, backward compatibility, or consumer-driven contract drift

Probes: catch blocks; fallback branches; adapter implementations; return-value checks; ignored promises/results; OpenAPI/GraphQL/protobuf schema changes.

Good proof: caller/callee or producer/consumer mismatch with concrete failure branch.

Reject when: error conversion is documented and all callers handle it.

Keywords: catch, fallback, null, partial, retry, idempotency, versioning, backward compatibility, adapter, contract drift.

## cache-persistence

Start from: cache keys, invalidation, TTL, stale reads, transactions, migrations, background jobs, outbox/inbox, message delivery.

Look for:

- cache key missing tenant, owner, locale, version, auth, filter, or feature-flag dimension
- write/delete not invalidating dependent reads
- stale read after write causing persisted or user-visible wrong behavior
- lost update, write skew, partial persistence, missing rollback
- duplicate/out-of-order message delivery not handled idempotently

Probes: cache `get/set/delete`; key builders; transaction boundaries; migrations/defaults; job retry logic; idempotency keys; message ordering assumptions.

Good proof: sequence of read/write/delete/event operations that leaves stale, duplicated, or inconsistent state.

Reject when: stale data is explicitly allowed and cannot escape its tolerance window.

Keywords: cache key, TTL, invalidation, stale, stampede, transaction, isolation, lost update, write skew, outbox, inbox, duplicate.

## security-boundaries

Start from: auth, ownership, tenant/project/user scoping, path/file access, deserialization, reflection, secrets, policy fallbacks.

Look for:

- confused deputy: trusted service uses caller-controlled resource or policy
- ownership/tenant check done before canonicalization or against wrong ID
- public route, job, webhook, or internal API bypasses permission path
- unsafe deserialization, path traversal, injection, secret exposure
- fallback policy that grants access on error, timeout, or missing config

Probes: auth middleware order; resource lookup filters; tenant/user/project IDs; path joins; deserializers; reflection/internal access; secret logging; policy catch blocks.

Good proof: actor-to-resource trace showing unauthorized access, data exposure, injection, or unsafe fallback.

Reject when: framework or caller guarantees the boundary and evidence confirms it.

Keywords: auth, ownership, tenant, project, scope, trust boundary, confused deputy, canonicalization, deserialization, path, secret, policy.
