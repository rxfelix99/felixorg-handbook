# operations_backups.md — felixOrg Backup Operations

This document describes **how backups are actually run**, not how backup software marketing claims they work.
If something here feels boring, good — boredom is a feature.

---

## Objectives

Backups in felixOrg exist to satisfy four requirements, in this order:

1. **Recoverability** — data can be restored without heroics
2. **Predictability** — behavior is understandable and repeatable
3. **Isolation** — backup failure does not corrupt the source
4. **Simplicity** — complexity is treated as a liability

“Fast” and “clever” do not make the list.

---

## Backup tiers and expectations

### Tier 0 — Finance (highest consequence)
- **Scope:** `fin11` VM and supporting data
- **Host:** RFiMac
- **Tolerance for loss:** Near zero
- **Tolerance for downtime:** Low

### Tier 1 — Working data
- **Scope:** DEVONthink working DBs, Calibre, active projects
- **Host:** RFStudio
- **Tolerance for loss:** Low
- **Tolerance for downtime:** Moderate

### Tier 2 — Archives
- **Scope:** Large archival datasets (e.g., `KSink`)
- **Tolerance for loss:** Low
- **Tolerance for staleness:** High

### Tier 3 — Media
- **Scope:** Movies, TV, learning videos
- **Tolerance for loss:** High
- **Backup priority:** Optional

---

## Primary mechanisms

### Time Machine (macOS-native)

**Used for:**
- RFiMac (Tier 0 and OS state)
- RFStudio (local recovery, convenience restores)

**Characteristics:**
- Continuous / automatic
- Versioned
- Local

**Limitations:**
- Not authoritative
- Not portable
- Not trusted as the *only* copy

Time Machine is a **seatbelt**, not a parachute.

---

### rsync over SSH (authoritative)

**Used for:**
- RFStudio → RFiMac
- RFStudio → external/archive targets
- Selected RFiMac data → offsite rotation

**Why rsync:**
- Transparent
- Inspectable
- Scriptable
- Fails loudly

Typical performance:
- ~100–110 MB/s
- Bottleneck: 1 GbE LAN, not disk I/O

This is the backbone of felixOrg backups.

---

## Data flow (canonical)

