# Archer AX10 v3 WAN PHY path

**Status:** CORROBORATED  
**Applies to:** TP-Link Archer AX10 v3 / AX1500 RTL9607C board  
**Hardware reference:** PhoebusBSP-6, Linux 6.18.39  
**Evidence:** stock-firmware board data + Phoebus hardware measurements + RTL8211F/FS documentation

## Summary

The Archer AX10 v3 WAN jack is not connected directly to an integrated copper
PHY inside the RTL9607C.

The board uses the RTL9607C integrated switch/MAC on **switch port 6**, routes
that port through **SDS0 / SGMII0**, and connects it to an external
**RTL8211F/RTL8211FS-family Gigabit Ethernet PHY**. The PHY then drives the
copper pairs through the Ethernet magnetics to the WAN RJ45 jack.

The verified topology is better represented as two separate paths: the packet
datapath and the PHY management bus.

```
                              Linux
                                │
                                │  RX/TX netdev mapping
                                │
                             eth0.8
                                │
                                │
                    ┌───────────▼───────────┐
                    │      RTL9607C         │
                    │                       │
                    │  integrated switch   │
                    │      port 6 / MAC    │
                    └───────────┬───────────┘
                                │
                                │  SDS0 / SGMII0
                                │  serial MAC↔PHY link
                                │
                    ┌───────────▼───────────┐
                    │ RTL8211F/FS-family    │
                    │ external GbE PHY      │
                    └───────────┬───────────┘
                                │
                                │  10/100/1000BASE-T MDI
                                │
                         ┌──────▼──────┐
                         │ magnetics / │
                         │ transformer │
                         └──────┬──────┘
                                │
                                │  copper pairs
                                │
                            WAN RJ45


        RTL9607C external-MDIO control plane

          GPIO 65 / MDC ───────────────┐
                                       ├────► RTL8211F/FS management
          GPIO 10 / MDIO ──────────────┘
```

The packet path and management path are independent. MDC/MDIO configures and
reads the PHY; user traffic does not flow over MDIO.

A critical consequence is that **copper link at the RTL8211F/FS does not prove
that packets can reach the RTL9607C switch**. The copper side can negotiate
successfully while SDS0 is still unconfigured and the switch sees zero ingress
octets.

## Board mapping

The stock TP-Link firmware records the WAN topology as:

```
WAN_PHY_PORT_SET="1:6"
```

Phoebus hardware testing independently matched the four LAN jacks to switch
ports 0 through 3 and the WAN path to port 6.

The Linux-facing and physical mappings should be read separately:

```
Linux logical mapping:

    eth0.8
       │
       └──── RX/TX mapping ────► RTL9607C switch port 6


Physical WAN datapath:

    RTL9607C switch port 6 / MAC
               │
               │ SDS0 / SGMII0
               ▼
       RTL8211F/FS-family PHY
               │
               │ 10/100/1000BASE-T MDI
               ▼
          magnetics
               │
               ▼
           WAN RJ45
```

Traffic is bidirectional; the arrows above show the architectural relationship,
not a one-way packet direction.

This is a **board-specific fact**. It must not be generalized to every RTL9607C
design.

## External PHY identification

The PHY was discovered by probing the board's external MDIO path rather than the
RTL9607C switch's internal port-indexed MIIM path.

Observed PHY ID:

```
PHYID1 = 0x001c
PHYID2 = 0xc916
```

Phoebus identifies this as an RTL8211F/FS-family PHY, consistent with the
Realtek RTL8211F/FS documentation used during bring-up.

The board-facing documentation and code generally refer to the device as
RTL8211F/FS because the PHY ID identifies the family more reliably than the
exact package/feature suffix. Where the exact physical variant matters, verify
it from the device marking or board BOM rather than inferring it from the PHY ID
alone.

## External MDIO

The WAN PHY does **not** respond through the RTL9607C switch's ordinary
port-indexed MIIM path.

The working external management bus discovered on hardware is:

```
MDC  = GPIO 65
MDIO = GPIO 10
```

During discovery the PHY responded at addresses 0 and 6.

The address behaviour needs to be interpreted with care. The RTL8211FS family
supports strap-selected PHY addressing and also has broadcast/management
behaviour that can make more than one probe address appear meaningful depending
on configuration. Documentation should therefore distinguish:

- the board's switch port number;
- the MDIO PHY address;
- any broadcast response.

They are separate namespaces.

### GPIO release requirement

The external MDIO pins were initially invisible because they were still owned
by the RTL9607C GPIO block.

On this platform, the alternate function becomes usable only after software
releases the relevant GPIO state. That is why early scans through the internal
MIIM path and through guessed external-MDIO pin pairs failed.

This was an important bring-up lesson: a missing MDIO response did not imply a
missing PHY.

## MAC-side interface

The relevant RTL8211FS documentation supports both RGMII and SGMII-family
applications and documents an SGMII-capable MAC/PHY interface.

The AX10 v3 uses the **SGMII path through RTL9607C SDS0**.

The key evidence is not just source naming. Hardware behaviour changed
immediately when SDS0 was configured:

```
before SDS0 configuration:
    PHY SerDes-side status = 0x6189
    switch port 6 in_octets = 0

after SDS0 configuration:
    PHY SerDes-side status = 0x61ad
    switch port 6 in_octets = 741158
```

The later state corresponded to link-up / completed in-band negotiation on the
SerDes side.

That is the strongest evidence that the PHY-to-switch path is SDS0/SGMII0 rather
than merely a naming convention in vendor source.

## Why copper link was misleading

The external RTL8211F/FS contains its own copper PHY.

That means the following can all be true at the same time:

- WAN cable plugged in;
- copper auto-negotiation complete;
- link partner LEDs active;
- PHY BMSR/physical status reports link;
- switch port 6 receives **zero** frames.

This is exactly what happened during Phoebus bring-up.

The missing piece was the MAC-side SerDes connection between the external PHY
and the RTL9607C switch. Until SDS0 was configured, packets could reach the
copper PHY but had no working path into the switch.

Therefore:

> **PHY copper link and RTL9607C WAN datapath readiness are separate states.**

This distinction should be preserved in diagnostics.

## SDS0 versus SDS1

The WAN uses **SDS0**.

An SDS1 experiment was performed on hardware and rejected:

- the PHY SerDes-side status did not improve;
- switch ingress remained dead;
- PCIe port 1 stopped linking;
- one Wi-Fi radio disappeared.

Vendor register handling shows that SGMII1 and PCIe port 1 share a selectable
lane on this SoC configuration.

The working rule for this board is therefore:

```
sds=0   -> WAN / switch port 6 / eth0.8
sds=1   -> do not use for WAN; conflicts with PCIe port 1
```

This lane conflict is currently documented as a **board/platform-observed
behaviour**. It should not be promoted into a universal RTL9607C package rule
without broader evidence.

## Linux-facing mapping

Once the hardware path was understood, Linux still required an explicit receive
mapping:

```
switch port 6 -> eth0.8
```

Transmit already used port 6 through the netdev mask, but ingress originally
fell through to the CPU root device rather than `eth0.8`.

That software mapping problem was distinct from the earlier SerDes problem.
Both had to be correct:

```
copper PHY link
    +
SDS0/SGMII0 link
    +
switch port 6 state
    +
port 6 -> eth0.8 receive mapping
    =
usable WAN datapath
```

## Diagnostic model

When WAN carrier is up but traffic does not work, debug from the PHY toward the
CPU rather than treating carrier as proof of the whole path.

### 1. Copper PHY

Check:

- BMCR/BMSR;
- negotiated speed/duplex;
- physical-status register;
- cable/link partner.

### 2. PHY SerDes side

Check the RTL8211F/FS SerDes/SGMII status and in-band auto-negotiation state.

A healthy copper link with a dead SerDes side indicates a PHY-to-switch problem,
not a cable problem.

### 3. RTL9607C switch port 6

Use per-port MIB counters.

Interpretation used during bring-up:

| Observation | Likely boundary |
|---|---|
| all ingress counters remain zero | frames are not reaching the switch MAC |
| `in_octets` rises with discards | switch received frames but rejected them |
| `in_octets` rises without discards | switch accepted traffic; debug switch-to-CPU path |

### 4. Linux netdev

Verify:

```
port 6 -> eth0.8
```

and inspect `eth0.8` RX/TX counters independently of switch MIB counters.

## PHY documentation used

The following Realtek documents were used as PHY-family reference material:

- **RTL8211F-CG / RTL8211FD-CG / RTL8211FI-CG / RTL8211FDI-CG Datasheet**,
  Rev. 1.9, 11 June 2018.
- **RTL8211FS-CG / RTL8211FS-VS-CG / RTL8211FSI-CG / RTL8211FSI-VS-CG
  Datasheet**, Rev. 1.8, 21 April 2021.
- RTL8211F-family reference schematic material.

The documents describe the PHY family, MAC-side interfaces, MDIO management,
strap configuration, reset/interrupt behaviour and RGMII/SGMII capabilities.

The PDFs are not mirrored here because duplicating the existing documentation
archive would add little value.

First check **jameywine's Realtek documentation repository**:

https://github.com/jameywine/realtek-doc

Search there for the exact document title or device family. If a document cited
by this project is not present in that repository, contact the maintainer at:

**sleazyconsumer@proton.me**

When requesting a document, include the exact title/revision referenced here so
similarly named Realtek revisions are not confused.

## Evidence chain

### Stock firmware / board data

TP-Link stock firmware:

```
WAN_PHY_PORT_SET="1:6"
LAN_PHY_PORT_SET="1:0 2:1 3:2 4:3"
```

This establishes the vendor board mapping.

### PhoebusBSP-6 commits

Key bring-up commits:

- `6b34e45f3021` — identifies WAN as switch port 6 from stock board data.
- `09277d6e35db` — discovers the external RTL8211F/FS-family PHY and the
  external MDIO pins.
- `fc4421089731` — maps switch port 6 receive traffic to `eth0.8` and adds
  per-port MIB diagnostics.
- `43de8ca7147e` — rejects SDS1 after hardware testing and records the PCIe
  lane conflict.
- `6f1121d77d8a` — configures SDS0 successfully; SerDes status changes and
  port 6 ingress counters begin moving.

These commits are especially valuable because the messages preserve the failed
hypotheses and the measurements that eliminated them.

## Confidence

| Claim | Classification |
|---|---|
| AX10 v3 WAN is switch port 6 | **CORROBORATED** |
| Linux WAN netdev is `eth0.8` | **VERIFIED** |
| External PHY is RTL8211F/FS family | **CORROBORATED** |
| External management uses MDC=65 / MDIO=10 | **VERIFIED** |
| WAN MAC-side link uses SDS0 / SGMII0 | **CORROBORATED** |
| SDS1 conflicts with PCIe port 1 on this platform | **VERIFIED** on tested board |
| Copper link alone does not prove WAN datapath readiness | **VERIFIED** |
| Exact F versus FS package suffix from PHY ID alone | **NOT ESTABLISHED** |

## Open questions

- Record the exact physical PHY package marking / board BOM if a definitive
  RTL8211F versus RTL8211FS suffix is required.
- Preserve the exact strap state and effective MDIO address behaviour in a
  future board schematic/BOM document.
- Document the RTL9607C SDS register path separately in the SoC networking
  reference rather than duplicating it here.
