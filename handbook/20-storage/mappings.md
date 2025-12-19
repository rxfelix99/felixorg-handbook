# storage_mappings.md — felixOrg Storage Mappings

This file describes **what lives where, and why**.  
It is not a performance benchmark and not a vendor comparison.  
It exists to prevent the two classic failures: “I forgot” and “I assumed.”

---

## Authoritative inventory

The **single source of truth** for storage devices, mounts, and ownership is:

- `assets/spreadsheets/network_assets.xlsx`
- Diff / normalized export: `assets/spreadsheets/network_assets.csv`

This Markdown file is interpretive and explanatory.  
If it disagrees with the spreadsheet, the spreadsheet wins.

---

## Storage tiers (intentional, not accidental)

### Tier 0 — Financial / irreplaceable
**Characteristics:** small, critical, audited, boring.

- Hosted on: **RFiMac**
- Primary VM: `fin11` (Windows 11, Quicken, finance records)
- Storage:
  - VM disk on internal SSD
  - Backed up via Time Machine and rsync
- Rules:
  - No experiments on this host
  - No “temporary” files
  - No convenience shortcuts

If this tier is corrupted, recovery must be predictable.

---

### Tier 1 — Working knowledge & production
**Characteristics:** actively used, high value, frequently accessed.

- Host: **RFStudio**
- Storage:
  - Internal SSD (4 TB) — primary working volume
- Major datasets:
  - DEVONthink 4 working database(s)
  - Calibre e-book library
  - Active writing, research, and project files
- Backup:
  - Time Machine (local)
  - rsync to RFiMac and/or external targets

This is where thinking happens. Speed matters, but integrity matters more.

---

### Tier 2 — Archives & cold data
**Characteristics:** large, stable, rarely modified.

- Host: **RFStudio**
- Device:
  - External HDD `KSink` (≈20 TB)
- Contents:
  - Large DEVONthink archival databases
  - Media archives
  - Historical project data
- Backup:
  - Periodic rsync
  - Manual verification

Archives are **write-rare, read-sometimes**.  
They do not belong on primary SSDs and do not need to be fast.

---

### Tier 3 — Media & consumption
**Characteristics:** replaceable, large, tolerant of loss.

- Host: **RFMini**
- Storage:
  - External or internal media volumes
- Contents:
  - Movies
  - TV
  - Learning videos
- Backup:
  - Minimal or none
  - Regenerated from source if lost

This tier exists so higher tiers don’t get polluted.

---

## Host-by-host mapping (current reality)

### RFStudio
| Volume | Type | Purpose | Notes |
|---|---|---|---|
| Internal SSD | NVMe | Tier-1 working data | DEVONthink, Calibre, active projects |
| `KSink` | External HDD | Tier-2 archive | Large, cold datasets |
| External SSD(s) | SSD | VMs / staging | Apple-silicon VMs, scratch |

### RFiMac
| Volume | Type | Purpose | Notes |
|---|---|---|---|
| Internal SSD | SSD | Tier-0 finance + OS | Hosts `fin11` VM |
| Time Machine | External HDD | Backup | Swapped offsite quarterly |
| Media.bak | External HDD | Backup target | rsync destination |

RFiMac is storage-conservative by design. If space fills up, something else moves — not finance.

### RFMini
| Volume | Type | Purpose | Notes |
|---|---|---|---|
| Media volume(s) | HDD/SSD | Tier-3 media | No critical data |

---

## Application-specific storage rules

### DEVONthink
- Working databases live on **RFStudio internal SSD**
- Large archival databases live on **KSink**
- Problematic file types (ISOs, VMs, raw disk images):
  - Stored in the filesystem, **not** indexed
- DEVONthink is a *knowledge system*, not a dumping ground

### Calibre
- Library hosted on **RFStudio**
- Actively curated (redundant formats removed)
- Indexed / cross-referenced in DEVONthink
- Backed up via rsync

### Virtual machines
- RFStudio (ARM):
  - Stored in `/Users/Shared/VMs`
  - Fast storage preferred, but rebuildable
- RFiMac (Intel):
  - VM disks stored internally
  - Finance VM treated as Tier-0 data

---

## Backup and data flow (summary)

