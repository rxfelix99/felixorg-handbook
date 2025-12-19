# hosts.md

## Purpose
A single source of truth for:
- Hostnames (human + machine)
- Roles (what breaks if it’s down)
- OS + hardware notes
- LAN IPs (192.168.50.0/24)
- Overlay/Mesh IPs (NordVPN Meshnet)
- Where VMs live and what they’re for

This file is meant to be edited frequently and used as input for diagrams, monitoring notes, and troubleshooting.

---

## Naming conventions
- **RFStudio** = primary workstation (Apple Silicon Mac Studio).
- **RFiMac** = Intel iMac used primarily to host Intel VMware VMs (especially `fin11`).
- **RFMini** = downstairs media Mac mini.
- **RFAirz** = newer MacBook Air (M4).
- **RFPro** = older 2017 13" MacBook Pro (Intel).
- Linux boxes are prefixed `rf-ubu*` (Ubuntu server/desktop).

Conventions:
- Hostnames in DNS/hosts are lowercase aliases (`rfstudio`, `rfimac`) even if the “marketing name” is title-case.
- VMs are listed explicitly; do **not** assume they appear in router DHCP or Meshnet.

---

## Address plan (current working assumptions)
- **LAN:** `192.168.50.0/24`
- **Router/gateway:** _(fill in)_ e.g., `192.168.50.1`
- **Static/reserved range:** _(fill in)_ e.g., `.50–.99` for infrastructure and desktops
- **Meshnet:** `100.x.x.x` addresses (unstable over time unless pinned; treat as “current observed”)

---

## Canonical host inventory

### Core Macs
| Hostname (alias) | “Proper” name | Role / Criticality | OS (expected) | LAN IP | Mesh IP (observed) | Notes |
|---|---|---:|---|---:|---:|---|
| `rfstudio` | RFStudio | Tier-1: primary work + research | macOS Tahoe 26.0.1 | `192.168.50.60` | `100.123.195.58` | Mac Studio (M3 Ultra), 256GB, 4TB. Hosts DEVONthink/Calibre. External archive HDD: `KSink`. |
| `rfimac` | RFiMac | Tier-0: **must** stay reliable for `fin11` | macOS Sequoia 15.7.1 | `192.168.50.50` | `100.82.81.204` | Intel iMac 2019. Primary job: VMware Fusion Intel VMs (`fin11`). Anything else is secondary. |
| `rfmini` | RFMini | Tier-2: media server | _(fill in)_ | `192.168.50.82` | `100.76.53.245` | Downstairs media box attached to LG TV. |
| `rfairz` | RFAirz | Tier-2: laptop/mobile | _(fill in)_ | _(LAN varies)_ | `100.80.116.14` | MacBook Air M4, 16GB/256GB. |
| `rfpro` | RFPro | Tier-3: legacy Intel laptop | macOS Ventura 13.7.6 | `192.168.50.200` | _(mesh varies)_ | 2017 13" MBP, dual-core i5, 8GB. |

### Linux hosts (Ubuntu)
| Hostname (alias) | Role / Criticality | OS | LAN IP | Mesh IP | Notes |
|---|---:|---|---:|---:|---|
| `rf-ubuserv` | Tier-2: services | Ubuntu Server (ARM?) | `192.168.50.70` | _(fill in)_ | Intended as a stable server node. Specify services below. |
| `rf-ubudesk` | Tier-3: desktop/test | Ubuntu Desktop (ARM?) | `192.168.50.80` | _(fill in)_ | Used for interactive Linux work/tests. |

### Special entries / aliases seen in `/etc/hosts`
| Alias | IP | What it is | Notes |
|---|---:|---|---|
| `localhost` | `127.0.0.1` / `::1` | local loopback | Standard. |
| `broadcasthost` | `255.255.255.255` | broadcast | Standard macOS entry. |
| `rfstudio-mesh` | `100.123.195.58` | Mesh alias for RFStudio | Should match RFStudio mesh. |
| `rfimac-mesh` | `100.82.81.204` | Mesh alias for RFiMac | Observed. |
| `rfimac-mesh` (duplicate?) | `100.80.120.119` | Mesh alias (duplicate) | This smells like stale data or copy/paste damage. Keep only one after validation. |
| `fin11` | `192.168.50.72` | VM (Windows 11 finance) | Runs on **RFiMac** under VMware Fusion (Intel). |
| `rxfelix-himalayas.nord` | `100.119.246.169` | NordVPN mesh name | Treat as “current observed.” |
| `rxfelix-alps.nord` | `100.85.21.143` | NordVPN mesh name | Treat as “current observed.” |

---

## Virtual machines

### VMware Fusion on RFiMac (Intel)
| VM name | Purpose | OS | Where | IP / Access | Notes |
|---|---|---|---|---|---|
| `fin11` | **Finance / Quicken** | Windows 11 | RFiMac (Fusion Intel) | `192.168.50.72` | Highest priority workload on RFiMac. Don’t do “fun experiments” on this host that risk stability. |

### VMware Fusion on RFStudio (Apple Silicon)
| VM name | Purpose | OS | Storage path | Notes |
|---|---|---|---|---|
| `win11-arm-fin` | (historical) finance VM | Windows 11 ARM | `/Users/Shared/VMs` | No longer used for finance tasks; kept only if needed. |
| `win11-arm-wks` | general workstation | Windows 11 ARM | `/Users/Shared/VMs` | ARM Windows for general tooling/tests. |
| _(others)_ | Ubuntu ARM VMs | Ubuntu | `/Users/Shared/VMs` | Prefer `open-vm-tools`. |

---

## Services and roles (fill in as you implement)
| Host | Service | Port(s) | Data location | Backup method | Notes |
|---|---|---|---|---|---|
| `rf-ubuserv` | _(e.g., SMB/NFS, Docker, monitoring)_ | _(fill in)_ | _(fill in)_ | _(fill in)_ | |
| `rfmini` | media serving | _(fill in)_ | _(fill in)_ | _(fill in)_ | |

---

## Operational notes / sharp edges
- `/etc/hosts` is **not** an inventory system; it’s a blunt instrument. Use it for convenience, but keep this file as the real “story.”
- Mesh IPs can change. If you need stability, document the *name* you connect to (or pin/assign if your mesh product supports it).
- The duplicate `rfimac-mesh` entry is either stale or wrong. Fix it once verified.

---

## Validation checklist (do this when you have 10 minutes)
- [ ] Confirm router gateway IP and note it in Address plan.
- [ ] Confirm whether `.50–.99` are DHCP reservations or truly static.
- [ ] On each host: record `scutil --get ComputerName`, `HostName`, `LocalHostName`.
- [ ] Confirm Meshnet identity mappings (device name → mesh IP).
- [ ] Confirm Linux host roles/services and whether they’re physical or VMs.

---

## Change log
- 2025-12-18: Initial best-guess template created from known felixOrg context and observed `/etc/hosts` entries.
