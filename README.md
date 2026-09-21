# rtk9607c-docs

Evidence-driven technical documentation for the **Realtek RTL9607C / RTL9607Cv2**
router/XPON SoC family.

This repository exists to turn scattered vendor source, board bring-up work,
runtime measurements, Git history and reverse-engineering notes into a durable
technical reference.

The immediate hardware reference is the **TP-Link Archer AX10 v3 / AX1500**
RTL9607C platform used by the Phoebus-OS project, but the documentation is
structured to keep **SoC behaviour**, **board wiring**, **vendor implementation
details** and **kernel-port behaviour** separate.

> This project is independent community documentation. It is not an official
> Realtek or TP-Link documentation repository.

## Purpose

The RTL9607C has useful implementation knowledge spread across vendor GPL
sources, downstream kernel code, bootloader data, board firmware, live hardware
experiments and the Phoebus BSP history.

The goal here is not to reproduce those trees. It is to answer questions such as:

- what hardware blocks exist and how are they connected?
- which switch port, SerDes lane, MDIO address or GPIO actually maps to a board
  function?
- which behaviour is inherent to the RTL9607C and which is board-specific?
- what does the vendor source imply, and what has been independently observed?
- which mainline Linux changes were required to keep the vendor hardware support
  working?
- where are the unknowns?

A useful document in this repository should make it possible to tell **what is
known, how it is known, and how strong the evidence is**.

## Evidence model

Documentation is classified by evidence strength rather than by how plausible a
claim sounds.

| Status | Meaning |
|---|---|
| **VERIFIED** | Directly observed on RTL9607C hardware. |
| **CORROBORATED** | Independent evidence agrees, typically hardware observation plus source/register evidence. |
| **SOURCE-DERIVED** | Established from RTL9607C vendor or project source, but not independently measured on hardware. |
| **INFERRED** | Supported by multiple observations, but not directly demonstrated. |
| **HYPOTHESIS** | Plausible working theory awaiting a discriminating test. |
| **RELATED-SOC** | Known from another Realtek family and used only as comparative context. |

These labels apply to **claims**, not entire files. A document may contain a
verified port map, a source-derived register interpretation and an open
hypothesis at the same time.

### Evidence types

The project will primarily draw from:

1. **Hardware observations** — boot logs, register reads, MIB counters, PHY
   dumps, packet tests, timing measurements, NAND dumps and controlled A/B tests.
2. **Git diffs** — strong evidence for exactly what implementation changed.
3. **Commit messages** — contemporary engineering notes containing intent,
   observations, failed approaches and test results.
4. **Current Phoebus source trees** — the implementation state of BSP-6, BSP-7
   and the shared SDK.
5. **Original/public vendor and GPL material** — provenance and baseline
   behaviour.
6. **Bootloader and stock-firmware evidence** — partition layouts, environment,
   scripts, calibration/configuration data and observed boot behaviour.
7. **Upstream Linux and related Realtek support** — API history and comparative
   implementation evidence, never silently treated as RTL9607C fact.

A Git diff proves that code changed. A commit message records what the author
believed or observed at that time. A runtime artifact demonstrates what the
hardware actually did. The strongest findings normally combine all three.

## Repository boundaries

This repository is the **knowledge base**.

It is deliberately not the place for moving kernel-status dashboards or copies
of entire vendor trees.

Implementation state belongs in:

- [PhoebusBSP-6](https://github.com/martiancomputer/PhoebusBSP-6) — Linux 6.18
  LTS hardware-reference BSP
- [PhoebusBSP-7](https://github.com/martiancomputer/PhoebusBSP-7) — current
  mainline forward-port
- [Phoebus-SDK](https://github.com/martiancomputer/Phoebus-SDK) — shared vendor
  source snapshot, rootfs, services and tooling

This repository extracts the durable hardware and engineering knowledge from
those projects while retaining links back to the evidence.

## Initial layout

The repository is intentionally starting small. The first stage is to establish
source provenance and documentation rules before importing technical findings.

```
rtk9607c-docs/
├── README.md
├── LICENSE
├── SOURCES.md
├── CONTRIBUTING.md
└── docs/
    └── README.md
```

The intended long-term documentation structure is:

```
docs/
├── soc/          RTL9607C architecture and on-SoC peripherals
├── networking/  switch, MAC, SerDes, MDIO, FleetConntrack
├── boards/       board-specific wiring and platform data
├── findings/     individual reverse-engineering investigations
├── software/     vendor BSP, Linux porting and device-tree notes
├── reference/    compact maps, tables and indexes
└── evidence/     curated logs, measurements and reproducible observations
```

Those directories should be created as material is ready for them rather than
filled with speculative placeholders.

## Documentation rules

A few rules are non-negotiable:

- **Do not manufacture completeness.** Unknown registers and unexplained bits
  stay unknown.
- **Separate board facts from SoC facts.** A TP-Link wiring decision is not an
  RTL9607C architectural property.
- **Separate vendor intent from hardware observation.** Source code can explain
  an implementation without proving the silicon behaves that way.
- **Preserve negative results.** A failed experiment can eliminate an entire
  class of explanations.
- **Prefer reproducible evidence.** Include the command, register, counter,
  commit or test condition needed to reproduce an important claim.
- **Keep historical claims historical.** A result from Linux 6.18 or 7.1 is not
  silently promoted to a current-kernel result.
- **Cite the evidence.** Important claims should point to their source, commit,
  log, measurement or finding document.

See [CONTRIBUTING.md](CONTRIBUTING.md) for the contribution and evidence format.

## Acknowledgements

### jameywine

A substantial part of the groundwork that made this project practical was done
by **[jameywine](https://github.com/jameywine)**.

The initial Phoebus RTL9607C research did not start from an empty field. It
benefited directly from jameywine's prior work collecting, organizing and
publishing hard-to-find Realtek material, including the Cudy GP3000 GPL source
and the `realtek-doc` collection. That work provided important source and
documentation context for RTL960x/RTL9607C investigation.

This is part of a broader body of work rather than a single repository. Public
work associated with jameywine includes:

- `GPL-for-GP3000`, publishing a modern Realtek/Cudy GPL source tree used as a
  major reference during the initial port;
- `realtek-doc`, a curated collection of Realtek SoC/PHY/NIC documentation and
  source, including RTL9607C application notes and RTL960x material;
- `RTL960x` and `realtek-user-sources`, additional RTL960x/Realtek research
  and source collections;
- ongoing OpenWrt work adding an **RTL9607C/RTL8198D subtarget** and related
  RTL960x support.

The OpenWrt work is especially relevant because it independently demonstrates
that this was sustained platform-enablement work: the public RTL9607C/RTL8198D
series includes SoC support and follow-up driver work for areas such as I2C,
thermal and clock control.

Phoebus-OS and this documentation repository are independent projects, and any
mistakes here are ours. But the project explicitly recognizes jameywine's work
as **foundational prior work**. Without that source preservation, curation and
platform research, the initial RTL9607C bring-up would have been substantially
harder and slower.

## Sources and provenance

[SOURCES.md](SOURCES.md) is the source ledger for the project.

The initial corpus now records the TP-Link AX10 v3 GPL release, Cudy GP3000
firmware, RTL8198D/RTL96xx comparative trees, Realtek MIPS RSDK/toolchain
collections, the jameywine/ProMix0 GP3000 lineage, collected Realtek
documentation and the still-to-be-pinned Chinese-language research material.

Source duplication is tracked explicitly. For example,
`jameywine/GPL-for-GP3000` and `ProMix0/rtl8198d-sdk` currently resolve to
the same Git commit and therefore count as one evidence lineage, not independent
corroboration.

The initial Linux 6.18 port was AI-assisted: the maintainer reports an
approximately four-hour continuous Claude Fable 5 synthesis/implementation
session over the collected corpus. The model output itself is not treated as a
source. The evidence is the input corpus, resulting Git diff and subsequent
hardware artifacts.

Third-party source material is **not relicensed** by this repository.

## License

The original documentation and project-authored material in this repository are
licensed under the [Apache License 2.0](LICENSE), unless a file explicitly says
otherwise.

Third-party code, documents, dumps and other source material retain their
original licenses and copyright. Where practical, this repository references
such material instead of copying it. See [SOURCES.md](SOURCES.md).

## Current phase

The repository is currently in the **source inventory and evidence extraction**
phase.

The initial source inventory is now populated. The next useful step is to pin
the remaining unresolved source URLs/hashes, then mine BSP-6, BSP-7 and the
shared SDK for findings whose evidence can be reconstructed from commits, diffs
and hardware observations.
