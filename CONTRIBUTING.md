# Contributing

Contributions are welcome when they improve the accuracy, reproducibility or
provenance of the RTL9607C documentation.

This is an evidence-driven hardware documentation project. A technically
interesting claim without a way to distinguish observation from assumption is
less useful than a smaller claim with a clear evidence trail.

## Before adding a finding

Decide what kind of statement you are making.

| Label | Use when |
|---|---|
| **VERIFIED** | The behaviour was directly observed on RTL9607C hardware. |
| **CORROBORATED** | Independent evidence agrees, usually hardware plus source/register evidence. |
| **SOURCE-DERIVED** | RTL9607C source establishes the implementation, but hardware was not independently tested. |
| **INFERRED** | Several facts support the conclusion, but it has not been directly demonstrated. |
| **HYPOTHESIS** | It is a testable explanation, not yet a conclusion. |
| **RELATED-SOC** | Evidence comes from another Realtek family and is included only for comparison. |

Do not upgrade a claim because it "makes sense." Upgrade it when new evidence
supports the stronger classification.

## Finding format

A focused reverse-engineering finding should normally start with:

```markdown
# Finding title

Status: CORROBORATED
Applies to: RTL9607C / <board if board-specific>
Verified on: <kernel / firmware / commit>
Evidence: hardware observation + source inspection

## Summary

One-paragraph result.

## Context

What was being investigated and why.

## Evidence

Exact observations, source locations, commits, register values, measurements or
logs.

## Experiment / reproduction

Enough information to repeat the useful part of the test.

## Interpretation

What the evidence supports and what it does not support.

## Open questions

Remaining unknowns or tests that would falsify the current interpretation.

## References

Commit IDs, source IDs from SOURCES.md, related documents and external links.
```

Not every reference page needs this full format. Compact maps and tables should
still carry provenance for non-obvious values.

## Using Git evidence

Git history is a major evidence source for this project.

### Diffs

A diff is strong evidence for implementation state.

Use it to support statements such as:

- a register write was introduced;
- a Kconfig option changed;
- a new API was called;
- a device-tree property was added;
- a compatibility shim changed between kernel versions.

A diff alone does **not** prove the hardware result attributed to the change.

### Commit messages

Commit messages are valuable contemporary engineering notes. They may preserve:

- failure symptoms;
- before/after measurements;
- register values;
- test conditions;
- rejected explanations;
- root-cause reasoning.

Treat those as authored observations unless independently corroborated. Preserve
the distinction between phrases such as "should fix", "built", "booted" and
"verified on hardware."

### Runtime evidence

Where available, pair implementation history with the runtime result:

```
commit -> diff -> boot/test artifact -> conclusion
```

That chain is the preferred form for high-confidence findings.

## Source citations

Use [SOURCES.md](SOURCES.md) for source collections and stable provenance.

When practical, a technical claim should cite the narrowest useful evidence:

- repository + commit;
- file + symbol;
- source ID + path;
- hardware log + test conditions;
- register dump + command;
- measurement + control.

Avoid vague citations such as "vendor SDK says."

## Board-specific versus SoC-specific

This distinction must remain explicit.

Examples:

- "Archer AX10 v3 routes WAN through switch port 6" is a **board fact**.
- "RTL9607C exposes SerDes configuration through X mapper/API" is a **SoC/vendor
  implementation fact**.
- "SDS1 conflicts with PCIe port 1 on this tested configuration" may require
  both SoC and board evidence before generalising it to every RTL9607C design.

If a fact has only been observed on one board, say so.

## Unknowns are allowed

Do not fill holes by extrapolation.

Good:

```
bit 7: verified enable
bits 6:4: unknown
bits 3:0: source-derived mode field
```

Bad:

```
bits 6:4: probably reserved
```

unless there is evidence that they are reserved.

A partial register map with provenance is more useful than a complete-looking
map containing invented fields.

## Measurements

Include enough test context to make measurements meaningful:

- board/revision;
- kernel/firmware and commit;
- topology;
- link rate;
- relevant boot arguments;
- radio channel/width where applicable;
- traffic direction;
- duration/sample count;
- comparison/control.

Do not present one environment's RF or network measurements as universal silicon
limits.

## Third-party material

Do not import a vendor tree, datasheet dump or other large third-party work into
this repository merely for convenience.

Prefer:

- source identifiers;
- links;
- hashes;
- file/symbol references;
- original analysis and factual summaries.

Third-party material retains its original license. See [SOURCES.md](SOURCES.md).

## Sensitive and device-unique data

Before committing logs or dumps, remove material that is irrelevant to the
technical finding and should not be public, such as:

- passwords or tokens;
- private keys;
- device-unique credentials;
- unnecessary personal data;
- board MAC addresses when the exact address is not part of the finding.

Do not sanitize away technically relevant structure; replace values consistently
when the relationship between them matters.

## Style

Prefer precise engineering prose over promotional language.

Good:

> Port 6 `in_octets` remained at zero until SDS0 was configured. After the
> configuration change, the counter increased under the same traffic test.

Avoid:

> The revolutionary SDS fix completely solved networking.

Use exact numbers when available, and label estimates as estimates.

## Licensing contributions

By contributing project-authored documentation or code, you agree that it may be
distributed under the repository's Apache License 2.0 unless the contributed
file explicitly and validly carries another compatible license.

Do not contribute third-party material unless its license permits the intended
use and its provenance is recorded.
