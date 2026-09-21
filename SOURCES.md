# Sources

This file is the provenance ledger for **rtk9607c-docs**.

The technical documentation should make it possible to trace important claims
back to the material or experiment that supports them. This file records the
source collections used by the project; individual documents should still cite
the specific file, commit, log, register dump or measurement where practical.

## Source policy

A source being listed here does **not** mean every statement in it is assumed
correct.

Vendor comments can be stale. Board code can contain configuration for other
products. Commit messages can contain hypotheses that were later disproved.
Related SoCs can share names while using different register layouts.

Sources are therefore used as evidence, not authority.

For each external source collection, record:

| Field | Meaning |
|---|---|
| **ID** | Stable short identifier used by documentation. |
| **Origin** | Project/vendor/author and where it came from. |
| **Version** | Release, tag, commit, archive name or date. |
| **Scope** | Which RTL9607C subsystems or board data it covers. |
| **License** | License attached to that material. |
| **Local use** | Reference only, compared against project code, extracted data, etc. |
| **Notes** | Reliability, board applicability and known caveats. |

## Project-controlled evidence

These repositories are first-party evidence for the Phoebus RTL9607C work.

### PHOEBUS-BSP6

- **Repository:** https://github.com/martiancomputer/PhoebusBSP-6
- **Role:** Linux 6.18 LTS hardware-reference BSP.
- **Useful evidence:** commit history, kernel diffs, configuration changes,
  hardware bring-up notes, runtime measurements and `PROJECT.md`.
- **Caveat:** documentation summaries can lag implementation history; prefer the
  underlying commit/diff/runtime evidence when they disagree.

### PHOEBUS-BSP7

- **Repository:** https://github.com/martiancomputer/PhoebusBSP-7
- **Role:** current-mainline forward-port of the RTL9607C BSP.
- **Useful evidence:** kernel API migrations, forward-port diffs, current
  mainline hardware tests, FleetConntrack work and mainline-specific failures.
- **Caveat:** a successful forward-port/build does not by itself prove hardware
  behaviour.

### PHOEBUS-SDK

- **Repository:** https://github.com/martiancomputer/Phoebus-SDK
- **Role:** shared vendor-source snapshot, rootfs, s6 services, wireless policy,
  provisioning and board tooling used by both BSPs.
- **Useful evidence:** vendor implementation, common userspace policy, board
  scripts, diagnostics and source provenance.
- **Caveat:** the SDK contains both vendor-derived material and project-authored
  modifications; provenance must be tracked at the relevant subtree/file level.

## External source inventory

**Pending.**

The original vendor/GPL/source collections used during the RTL9607C work will be
added here once their exact names, versions and origins are supplied.

Do not guess archive names, vendors, release versions or licenses to fill this
section.

Suggested entry format:

```markdown
### SOURCE-ID — descriptive name

- **Origin:**
- **Version / archive / commit:**
- **Obtained from:**
- **License:**
- **Relevant paths:**
- **Used for:**
- **Board / SoC applicability:**
- **Redistribution status:**
- **Notes:**
```

## Hardware-generated evidence

Runtime artifacts generated from owned test hardware are distinct from external
source material.

Examples include:

- serial boot logs
- `/proc` and `/sys` diagnostic output
- switch and PHY register reads
- MIB counter snapshots and deltas
- controlled throughput/latency measurements
- NAND reads and partition-layout observations
- PCIe enumeration
- GPIO/MDIO probes
- packet captures

When a document relies on one of these, record enough context to reproduce or
interpret it:

```
Board:
Kernel/build:
Commit:
Boot arguments:
Test conditions:
Command/tool:
Observed result:
Control/comparison:
```

Raw artifacts should only be committed when doing so is useful, lawful and free
of secrets or device-unique private data.

## Git history as evidence

Git history is intentionally treated as an engineering source.

Use it carefully:

- **diff:** proves what implementation changed;
- **commit message:** records contemporary intent, observations and reasoning;
- **later runtime result:** determines whether the proposed change actually
  behaved as expected on hardware.

A statement from a commit message that says "should fix" is not equivalent to a
later hardware result saying "verified."

## Licensing and redistribution

The repository's Apache-2.0 license applies to project-authored documentation and
other original material unless stated otherwise.

External material retains its original copyright and license. Listing it here
does not relicense it.

Prefer links, precise source identifiers and derived factual descriptions over
copying third-party source or documentation into this repository. If material
must be redistributed, its license and redistribution terms must be checked
first.
