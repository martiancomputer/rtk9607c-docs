# Documentation

This directory will contain the technical RTL9607C knowledge base.

The repository is currently in the **source inventory and evidence extraction**
phase. Subdirectories will be created as evidence-backed material is ready rather
than populated with speculative placeholders.

Planned organization:

```
docs/
├── soc/          SoC architecture and integrated peripherals
├── networking/  switch, Ethernet, SerDes, MDIO and FleetConntrack
├── boards/       board-specific wiring, flash layouts and platform behaviour
├── findings/     focused reverse-engineering investigations
├── software/     vendor BSP, Linux porting and device-tree documentation
├── reference/    compact tables, maps and indexes
└── evidence/     curated reproducible measurements and runtime artifacts
```

See the top-level [README](../README.md) for project scope,
[SOURCES.md](../SOURCES.md) for provenance, and
[CONTRIBUTING.md](../CONTRIBUTING.md) for evidence and writing rules.
