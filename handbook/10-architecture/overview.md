# overview.md — felixOrg Architecture Overview

## Purpose

felixOrg exists to support **work that must keep going**:
- Research, writing, and technical experimentation
- Long-term knowledge retention (DEVONthink, Calibre, journals)
- Media and creative work
- Personal finance and records (strictly isolated)

This is not a “home lab for tinkering.”  
It is a **personal production environment** that happens to evolve.

Primary goals:
- Predictability over novelty
- Recoverability over cleverness
- Documentation over memory
- Manual control over opaque automation

If something breaks, the system should explain *why* before it wastes time.

---

## High-level topology

At a high level, felixOrg is a **hub-and-spoke workstation-centric network**, not a server-centric one.

### Logical view

- **RFStudio** (Mac Studio, Apple Silicon)  
  → Primary thinking, writing, research, AI, and archive interaction node  
  → Hosts “working” datasets and ARM-native VMs

- **RFiMac** (Intel iMac)  
  → Legacy and compatibility anchor  
  → Hosts Intel VMware VMs, especially the finance VM (`fin11`)  
  → Treated as a *reliability appliance*, not a playground

- **RFMini** (Mac mini)  
  → Media consumption and light serving  
  → Deliberately kept out of critical workflows

- **Linux nodes (`rf-ubu*`)**  
  → Service or experimentation roles  
  → Explicitly named, explicitly documented  
  → No silent “pet servers”

- **Laptops (RFAirz, RFPro)**  
  → Access points, not authorities  
  → Never the only copy of anything important

### Physical / network view

- Flat LAN (`192.168.50.0/24`) behind a consumer ASUS mesh router
- No internal VLANs
- No exposed inbound WAN services
- NordVPN Meshnet overlays remote access as a parallel plane

This keeps failure modes understandable and debuggable.

---

## Naming conventions

Names are architecture. Sloppy names equal sloppy thinking.

### Hostnames
- **RFStudio** — primary workstation
- **RFiMac** — Intel compatibility + finance VM host
- **RFMini** — media system
- **rf-ubuserv / rf-ubudesk** — Ubuntu server / desktop
- **RFAirz / RFPro** — laptops

Rules:
- Lowercase aliases in DNS and `/etc/hosts`
- Names encode *role*, not ego
- If a system’s role changes, rename it

### Virtual machines
- Named for function first, OS second
  - `fin11` = finance, Windows 11
  - `win11-arm-wks` = Windows 11 ARM workstation
- VMs are documented where they live and why
- Finance VMs are never “temporary”

### Storage
- Names imply **purpose**, not capacity
  - `KSink` = archive sink
  - `Media`, `Media.bak` = obvious pairing
- A drive without a README is an unfinished task

---

## Key principles

### 1. Separation by consequence, not ideology
- Finance is isolated because mistakes there matter more.
- Media is loose because mistakes there are cheap.
- Research can break; archives must not.

### 2. Fewer moving parts beat clever automation
- rsync over SSH beats exotic backup stacks.
- `/etc/hosts` beats running DNS you’ll forget how to fix.
- If a tool needs babysitting, it better earn it.

### 3. Workstations are first-class citizens
This is not a rack of servers pretending to be a data center.
The **human sits at RFStudio**, so RFStudio is the hub.

### 4. Documentation is part of the system
- If it isn’t written down, it isn’t real.
- Markdown lives in git because memory lies.
- Diagrams are outputs, not truths.

### 5. Reversibility over optimization
- Changes should be undoable.
- Migration paths are documented before migration starts.
- “Fast” is irrelevant if rollback is painful.

### 6. Trust nothing implicitly
- LAN ≠ safe
- VPN ≠ trusted
- Automation ≠ correct

Everything earns its place by behaving well over time.

---

## Closing note

felixOrg is intentionally **boring where it matters** and flexible where it doesn’t.  
That’s not a lack of ambition — it’s how systems survive contact with real life.

---

## Change log
- 2025-12-18: Initial architecture overview synthesized from existing felixOrg practices and constraints.
