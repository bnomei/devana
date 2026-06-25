DEVANA-FINDING: v1
DEVANA-STATE: open | P2 | medium | security=no
DEVANA-KEY: path/to/file.ext:123 | short-slug

# Short Title

## Finding

Describe the concrete issue in plain language.

## Violated Invariant Or Contract

Name the expected behavior that appears to be broken.

## Oracle

Name the evidence source: docs, schema, tests, caller/callee contract, neighboring implementation, or implicit safety expectation.

## Counterexample

Give the smallest concrete value, state, event sequence, or failure path that could trigger the bug.

## Why It Might Matter

Explain likely impact without overstating certainty.

## Proof

List the dataflow trace, control-flow trace, counterexample value, contract mismatch, cross-entry mismatch, or state transition mismatch that makes this actionable.

## Counterevidence Checked

List guards, callers, config, framework behavior, transactions, cleanup, and the strongest reason this might be false. Explain why that counterevidence does not prevent the bug.

## Suggested Next Step

Describe the smallest useful human follow-up.

## Agent Handoff

After working this report, preserve the original finding body. Update line 2 `DEVANA-STATE: ...` and the final `DEVANA-SUMMARY:` status/priority/confidence prefix. Use one of: `open`, `fixed`, `invalid`, `stale`, `duplicate`, `wontfix`. Keep `DEVANA-KEY:` stable unless the same finding moved. Add dated notes below with evidence checked.

## Status Notes

- YYYY-MM-DD: open by Devana. Initial report written from static source inspection.

DEVANA-KEY: path/to/file.ext:123 | short-slug
DEVANA-SUMMARY: open | P2 | medium | One sentence explaining the bug and likely impact.
