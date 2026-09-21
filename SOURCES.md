# Sources

This file is the provenance ledger for **rtk9607c-docs**.

The goal is not merely to list things that were read during the port. It is to
make important technical claims traceable back to a source collection, Git
commit, hardware artifact, or experiment.

## Source policy

A source being listed here does **not** mean every statement in it is assumed
correct.

Vendor comments can be stale. Board code can contain configuration for other
products. Public mirrors can carry unclear licensing. Commit messages can
contain hypotheses that were later disproved. Related SoCs can share names while
using different register layouts.

Sources are evidence, not authority.

### Provenance state

Source metadata uses three states:

| State | Meaning |
|---|---|
| **VERIFIED-PUBLIC** | Public repository/source identity was independently located and pinned. |
| **MAINTAINER-RECORDED** | Origin or role comes from the project maintainer's acquisition notes; exact public URL/hash is still pending. |
| **UNRESOLVED** | The local artifact is known, but its exact upstream origin has not yet been established. |

This classification describes **source provenance**, not the truth of technical
claims derived from that source.

## Project-controlled evidence

These repositories are first-party evidence for the Phoebus RTL9607C work.

### PHOEBUS-BSP6

- **Repository:** https://github.com/martiancomputer/PhoebusBSP-6
- **Role:** Linux 6.18 LTS hardware-reference BSP.
- **Useful evidence:** commit history, kernel diffs, configuration changes,
  hardware bring-up notes, runtime measurements and `PROJECT.md`.
- **Caveat:** summaries can lag implementation history; prefer the underlying
  commit/diff/runtime evidence when they disagree.

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
- **Caveat:** it contains both vendor-derived material and project-authored
  modifications; provenance must be tracked at subtree/file level.

## Initial research corpus

The following collections formed the source corpus used during the initial
RTL9607C Linux 6.18 port. They are recorded separately even where they overlap,
because acquisition history matters.

### AX10V3-GPL — TP-Link Archer AX10 v3 GPL source

- **State:** MAINTAINER-RECORDED
- **Local archive:** `AX10v3_US_GPL.tar.gz`
- **Extracted directory:** `AX10v3_GPL/`
- **Origin:** TP-Link GPL source release for the Archer AX10 v3.
- **Role:** closest board-specific vendor source in the initial corpus.
- **License:** published as a GPL source package; individual files may carry
  additional or different notices and must be handled file-by-file.
- **Redistribution:** do not mirror the archive here. Reference it and record an
  official download URL/hash when available.
- **Pending:** exact official download URL and archive hash.

### CUDY-GP3000-STOCK — Cudy GP3000 stock firmware

- **State:** MAINTAINER-RECORDED
- **Files:**
  - `GP3000-4.2.2-20251110-015457-flash.bin`
  - `m_upgrade_GP3000-4.2.2-20251110-015457-flash_22384.zip`
- **Origin:** official Cudy website.
- **Role:** stock binary/upgrade reference for another device in the same
  broader Realtek platform ecosystem.
- **License:** binary firmware; no redistribution assumption is made here.
- **Redistribution:** reference-only unless Cudy's terms explicitly permit more.
- **Pending:** exact official URLs and hashes.

### GP3000-GPL-JAMEYWINE — jameywine GP3000 source tree

- **State:** VERIFIED-PUBLIC
- **Repository:** https://github.com/jameywine/GPL-for-GP3000
- **Pinned evidence head observed during source inventory:** `5090fbcb6ba743dd7b1314811ef557bad0460147`
- **Local archive/directory:** `GPL-for-GP3000-main.zip` /
  `GPL-for-GP3000-main/`
- **Role:** prior public attempt/reference used during research.
- **License:** no repository-wide license is assumed; obey file-level notices.
- **Important:** this is **not independent corroboration** from
  `ProMix0/rtl8198d-sdk` at the pinned state below: both repositories currently
  resolve to the exact same Git commit SHA.

### RTL8198D-SDK-PROMIX — ProMix0 RTL8198D SDK tree

- **State:** VERIFIED-PUBLIC
- **Repository:** https://github.com/ProMix0/rtl8198d-sdk
- **Pinned evidence head observed during source inventory:** `5090fbcb6ba743dd7b1314811ef557bad0460147`
- **Local directory:** `rtl8198d-sdk-main/`
- **Role:** RTL8198D/Realtek SDK comparison source.
- **License:** no repository-wide license is assumed; obey file-level notices.
- **Relationship:** same Git object/history state as
  `jameywine/GPL-for-GP3000` at the pinned head, so count the pair as **one
  evidence lineage**, not two independent sources.

### ELFS-RTL8198D — extracted RTL8198D ELF/reference corpus

- **State:** VERIFIED-PUBLIC
- **Repository:** https://github.com/kenjiuno/elfs-rtl8198d
- **Pinned evidence head observed during source inventory:** `8f100187e5d0fd0618de130c52f84f50dc488d5b`
- **Local directory:** `elfs-rtl8198d-master/`
- **Role:** comparative binary/ELF material for RTL8198D.
- **License:** no repository-wide license is assumed from the repository root.
- **Use:** comparative evidence only unless a specific file is shown to apply to
  RTL9607C.

### RTS3903N-RSDK — Realtek MIPS RSDK/toolchain collection

- **State:** VERIFIED-PUBLIC
- **Repository:** https://github.com/code-explorer1234/RTS3903N-rsdk-4.8.5-5281
- **Pinned evidence head observed during source inventory:** `a3765defbcb167ce461c5fa415b05f41f236d24a`
- **Local parent directory:** `RTS3903N-rsdk-4.8.5-5281-master/`
- **Contained SDK/toolchain directories:**
  - `rsdk-4.8.5-5281-EL-3.10-u0.9.33-m32fut-161202/`
  - `rsdk-6.4.1-5281-EL-4.9-u1.0-m32fut-180611/`
- **Role:** compiler/toolchain and older Realtek MIPS SDK context.
- **Relationship:** the repository's own README explicitly identifies those two
  RSDK releases as the included SDKs; the two extracted directories therefore
  come from one parent source collection.
- **License:** no repository-wide license is assumed; inspect individual
  components before redistribution.

### RTL960X-TOOLCHAIN — RTL9603C/RTL9607C toolchain collection

- **State:** VERIFIED-PUBLIC
- **Repository:** https://github.com/copslock/rtl9603c-rtl9607c-toolchain
- **Pinned evidence head observed during source inventory:** `2333970baa968be661ca528f4ffd56af7699c379`
- **Local directory:** `rtl9603c-rtl9607c-toolchain/`
- **Repository note:** its README identifies the content as a Netis GPL tarball
  source / `rtl819x-sdk-v1.2`.
- **Role:** historical toolchain/SDK context.
- **License:** do not infer a blanket license from the repository name; preserve
  the licenses/notices carried by the underlying components.

### RTL96XX-COLLECTION — `rtl96xx` source collection

- **State:** VERIFIED-PUBLIC
- **Repository:** https://github.com/hades2013/rtl96xx
- **Pinned evidence head observed during source inventory:** `2adbb7c13787ce370f911934c275e2c8c6a0e5f2`
- **Local archive/directory:** `rtl96xx-master.zip` / `rtl96xx-master/`
- **Role:** RTL96xx-family comparative source containing kernel, application,
  platform and documentation material.
- **Use:** comparative evidence unless a specific file/path is shown to apply
  directly to RTL9607C.
- **License:** do not infer a repository-wide license; preserve file-level
  notices and source provenance.

### JAMEYWINE-REALTEK-DOCS — collected Realtek documentation

- **State:** VERIFIED-PUBLIC
- **Repository:** https://github.com/jameywine/realtek-doc
- **Pinned evidence head observed during source inventory:** `e17db159796ec389651516d68351d166988978da`
- **Local directory:** `Jameywine/`
- **Role:** architecture/SoC/PHY/driver research supplied to the initial porting
  session.
- **Repository scope:** curated Realtek documentation and source covering Otto
  switch families and RTL960x/Apollo material. Its README explicitly lists
  **RTL9607C application notes** among the available PDFs and identifies the
  Cudy GP3000 GPL source as containing RTL960x/Cortina code.
- **Use:** source/documentary evidence at the individual file level; do not
  treat every document in the collection as RTL9607C-specific.
- **License:** no blanket repository-wide license is assumed for the collected
  third-party documents and source; preserve each item's original terms.

### CN-FORUM-DOCS — Chinese-language forum/document research

- **State:** MAINTAINER-RECORDED
- **Origin:** deep manual research across Chinese forums and linked documents.
- **Role:** additional RTL9607C/Realtek SoC and FleetConntrack behavioural
  context used during the initial port.
- **Pending:** exact thread/document titles, URLs, dates and local filenames.
- **Rule:** until each item is individually identified, this collection can
  explain research provenance but should not be used as a precise citation.

## Initial 6.18 port research lineage

The maintainer reports that the initial Linux 6.18 port was produced during an
approximately four-hour continuous **Claude Fable 5** analysis/implementation
session over the corpus above plus the collected documentation.

That model session is **not an independent technical source**.

For documentation purposes the evidence chain is:

```
source corpus
    -> AI-assisted synthesis / implementation
    -> resulting source diff
    -> hardware boot/test artifacts
    -> later corrective commits and measurements
```

Claims should cite the underlying source, resulting Git diff or runtime evidence,
not "the model said so."

## Hardware-generated evidence

Runtime artifacts generated from owned test hardware are distinct from external
source material.

### HW-BOOT-6.18.39-FIRST — initial 6.18.39 boot capture

- **Artifact:** `docs/evidence/boot-logs/first-6.18.39-boot.txt`
- **Chronology:** identified by the maintainer as the first boot attempt of the
  newly produced 6.18 port.
- **What the log directly verifies:** a Linux 6.18.39 legacy uImage passes its
  checksum, identifies the RTL9607C machine, brings up four CPUs, reaches
  `/init`, starts the minimal BusyBox rootfs and reaches a shell.
- **What it does not verify:** working LAN/WAN/Wi-Fi datapaths, flash
  installation, or successful FleetConntrack hardware acceleration.
- **Notable negative evidence:** the log also records switch/link interrupt
  failures, FleetConntrack API registration failure, and USB probe failures;
  reaching a shell must not erase those failures from the historical record.
- **Original uploaded log SHA-256:**
  `d4ebb2e283da4387341e5255345e4894f87717e535eb2ec3a37c5e3aa2dcab6c`
- **Committed sanitized log SHA-256:**
  `82272a921a6cae953f76abb0a45521cbc61c1d51fa15994c1510acff413ecb31`
- **Sanitization:** build username/hostname removed. No IPv4 addresses were
  present in this capture, so no ISP DHCP lease was retained.

Other useful hardware evidence includes serial logs, `/proc` and `/sys`
diagnostics, switch/PHY register reads, MIB counter deltas, controlled
throughput/latency measurements, NAND reads, PCIe enumeration and packet
captures.

When adding such evidence, record enough context to reproduce or interpret it:

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

Raw artifacts should only be committed when useful, lawful and free of secrets
or unnecessary device-unique/private identifiers.

## Git history as evidence

Git history is intentionally treated as an engineering source.

- **diff:** proves what implementation changed;
- **commit message:** records contemporary intent, observations and reasoning;
- **later runtime result:** determines whether the proposed change actually
  behaved as expected on hardware.

A commit message saying "should fix" is not equivalent to a later hardware result
saying "verified."

## Licensing and redistribution

The repository's Apache-2.0 license applies to project-authored documentation and
other original material unless stated otherwise.

External material retains its original copyright and license. Listing it here
does not relicense it, and public availability is not by itself evidence of
redistribution permission.

Prefer links, exact source IDs, hashes, commit IDs and original factual analysis
over copying third-party SDKs, binaries or documentation into this repository.
