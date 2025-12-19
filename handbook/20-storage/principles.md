# storage_principles.md — felixOrg Storage Principles

This document explains **why** storage in felixOrg is arranged the way it is.  
If storage mappings answer *where*, this answers *why that decision wasn’t stupid* at the time.

These principles are stable. Implementations can change.

---

## 1. Purpose beats capacity

Storage is chosen and named by **what it is for**, not how big or fast it is.

- “Archive” is a purpose.
- “Finance” is a purpose.
- “Media” is a purpose.

Capacity, interface, and speed are secondary attributes.  
If a volume’s purpose is unclear, it is misconfigured.

---

## 2. Consequence-based tiering

Data is tiered by **what happens if it is lost or corrupted**, not by how often it’s accessed.

| Consequence of loss | Tier | Example |
|---|---|---|
| Catastrophic / legal | Tier 0 | Finance (`fin11`) |
| Serious disruption | Tier 1 | DEVONthink working DB |
| Annoying but survivable | Tier 2 | Archives |
| Trivial | Tier 3 | Media |

If two datasets have different consequences, they do not belong together — even if they “fit.”

---

## 3. Fast storage is for thinking, not hoarding

- SSDs are for:
  - Active work
  - Indexing
  - Search-heavy datasets
- HDDs are for:
  - Bulk
  - History
  - Things you read more than you write

Putting archives on SSDs is not “future-proofing.”  
It’s wasting money and increasing blast radius.

---

## 4. Archives are write-rare, not write-never

Archives:
- Change slowly
- Grow deliberately
- Are verified, not casually edited

An archive that is constantly modified is not an archive — it’s a messy working set pretending to be organized.

---

## 5. Index selectively, not indiscriminately

Indexing is a privilege, not a right.

- Text, PDFs, structured knowledge → index
- ISOs, VM disks, raw media blobs → **do not index**

If a tool crashes when indexing something, that’s not a challenge — it’s a warning.

---

## 6. Redundancy must be intentional

Redundancy is useful only when you know:
- Why it exists
- Where the authoritative copy lives
- How divergence is detected

“Multiple copies” without authority is **data drift**, not safety.

---

## 7. Backups are pull-based and boring

- rsync over SSH
- Time Machine where appropriate
- No exotic snapshot stacks unless they earn their keep

Backups should:
- Be understandable without documentation
- Be verifiable by inspection
- Fail loudly

Silent failure is worse than no backup.

---

## 8. Rebuildable data is treated differently

Some data is precious. Some is just inconvenient to regenerate.

Examples of rebuildable data:
- Media downloads
- Derived files
- Many VMs
- Transcoded outputs

Rebuildable data:
- Gets weaker backup guarantees
- Is allowed to live on cheaper storage
- Is documented so it *can* be rebuilt

---

## 9. Documentation is part of the storage system

A disk without:
- A README
- A known owner
- A stated purpose

…is an accident waiting to happen.

Documentation:
- Lowers recovery time
- Prevents emotional decisions
- Outlives memory

If future-you has to guess, present-you failed.

---

## 10. Avoid cleverness that hides state

Storage systems should make state **obvious**:
- Where is the data?
- Who owns it?
- What depends on it?

Opaque layers (over-automation, silent sync, magical cloud mirroring) are rejected unless they can be fully reasoned about under failure.

---

## 11. Migration before optimization

No storage change is “simple” unless:
- Rollback is defined
- Old data remains intact until verified
- New paths are documented immediately

Optimizing first and planning later is how data gets lost.

---

## 12. Storage decisions are revisitable — assumptions are not

It is acceptable to change:
- Disk models
- Mount points
- Physical hosts

It is **not** acceptable to forget:
- Why the original decision was made
- What risk it was managing

That context is preserved here.

---

## Closing rule (the one that matters)

> If losing this data would ruin your week,  
> it deserves its own tier, its own backup plan,  
> and its own explanation.

Everything else is implementation detail.

---

## Change log
- 2025-12-18: Initial storage principles articulated from existing felixOrg practice and failure-avoidance patterns.
