```markdown
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

```

RFStudio (Tier 1)  ──rsync──▶  RFiMac (backup target)
RFStudio (Tier 2)  ──rsync──▶  RFiMac / external HDD
RFiMac (Tier 0)    ──TM─────▶  Local TM disk
RFiMac (Tier 0)    ──rotate──▶ Offsite TM disk (quarterly)

```

Pull-based where possible. Push-based only when unavoidable.

---

## rsync conventions

### General rules
- Always use **Homebrew rsync** where available
- Always use `--dry-run` for new jobs
- Always log output

### Canonical options (baseline)
Typical flags (illustrative, not magical):

- `-a` — preserve structure
- `-v` — visibility
- `--delete` — enforce authority (used cautiously)
- `--numeric-ids` — avoid UID surprises (Linux targets)
- `--stats` — sanity checks

If a flag’s purpose isn’t understood, it doesn’t belong in production.

### Authority rules
- Source is authoritative unless explicitly stated
- Backup targets do not originate data
- One-way sync unless a workflow explicitly requires otherwise

Bidirectional sync is a last resort and treated as hostile territory.

---

## Scheduling and cadence

### Tier 0 (Finance)
- Time Machine: continuous
- Offsite TM disk: rotated quarterly
- Manual verification after rotation

### Tier 1 (Working data)
- rsync: manual or semi-regular (weekly or before major changes)
- Time Machine: continuous local safety net

### Tier 2 (Archives)
- rsync: infrequent (monthly or after significant additions)
- Verification favored over frequency

### Tier 3 (Media)
- Opportunistic or none
- Rebuild from source if lost

---

## Verification and testing

Backups are **assumed broken until proven otherwise**.

Verification methods:
- Spot-restore files to a temp location
- Mount and boot critical VMs (finance)
- Compare rsync stats over time
- Check logs for silent failures

A backup that has never been tested is a hope, not a plan.

---

## Failure handling

### Expected failures
- Network interruption
- External disk asleep or unplugged
- Permission mismatches

Response:
- Fix cause
- Re-run
- Verify delta

### Unacceptable failures
- Silent truncation
- Partial success without error
- Overwriting authoritative data

If this happens, stop and investigate before continuing.

---

## Anti-patterns (explicitly rejected)

- “Set and forget” backups
- Cloud-only primary backups
- GUI-only backup tools with opaque state
- Bidirectional sync for critical data
- Assuming Time Machine “has it covered”

These fail badly when they fail.

---

## Documentation requirements

Every backup relationship must be documented:
- Source
- Destination
- Authority
- Cadence
- Restore procedure (brief)

If restoring requires guesswork, the backup is incomplete.

---

## Future considerations (parked, not forgotten)

- Offsite rsync over Meshnet (selective)
- Immutable backup targets
- Periodic checksum-based verification
- Snapshotting archives before major reorganizations

None are adopted until they demonstrably reduce risk without increasing fragility.

---

## Closing rule

> If you cannot explain how to restore it at 2 a.m.  
> you do not have a backup.

---

## Change log
- 2025-12-18: Initial backup operations document synthesized from current felixOrg rsync, Time Machine, and host-role practices.
```
