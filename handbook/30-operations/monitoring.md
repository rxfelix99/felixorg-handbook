```markdown
# operations_monitoring.md — felixOrg Monitoring & Situational Awareness

This document covers **how felixOrg knows when something is wrong** — or at least wrong enough to care.
This is not enterprise observability. It is **intentional awareness without noise**.

If monitoring becomes a second job, it has failed.

---

## Objectives

Monitoring in felixOrg exists to answer four questions quickly:

1. Is anything **broken** right now?
2. Is anything **about to break**?
3. If it breaks, **where do I look first**?
4. Did a recent change make things worse?

Anything that does not help answer those questions is optional.

---

## Monitoring philosophy

### Minimalism by design
- No always-on dashboards
- No alert storms
- No metrics collected “just in case”

Instead:
- Periodic checks
- High-signal indicators
- Human-in-the-loop awareness

You are the alerting system. The tools support you — they don’t replace judgment.

---

## What is actively monitored

### Host availability (Tier-based)

| Host | Tier | Monitoring method | Expectation |
|---|---:|---|---|
| RFiMac | Tier 0 | Human check + SSH | Must be reachable and stable |
| RFStudio | Tier 1 | Human check | Should be reachable daily |
| rf-ubuserv | Tier 2 | SSH | Should be reachable when needed |
| RFMini | Tier 3 | Ignore unless missing | Media outages are acceptable |

No ping sweeps. If you can’t SSH, that’s the signal.

---

### Storage health

#### macOS systems
- Disk Utility (manual, periodic)
- SMART status via Disk Utility or `diskutil`
- Free space sanity checks (especially RFiMac)

Red flags:
- Unexpected growth
- Repeated fsck repairs
- DEVONthink or Calibre stalls due to I/O

#### External HDDs
- Spin-up delays
- Repeated dismounts
- Read/write stalls during rsync

If an HDD starts “acting weird,” assume it is already dying.

---

### Backup health

Monitored by:
- Reviewing rsync output
- Watching transfer rates and deltas
- Spot-checking restored files

Signals:
- rsync suddenly much faster or slower
- Zero-byte transfers when changes were expected
- Growing error counts

Backups are monitored **by reading logs**, not by green checkmarks.

---

### Virtual machines

#### Finance VM (`fin11`)
- Boots cleanly
- Quicken opens and data is present
- No unexplained pauses or disk warnings

This VM is periodically **used on purpose** as a health check.

#### Other VMs
- Treated as disposable unless documented otherwise
- Failure is acceptable; silent corruption is not

---

### Network health

Monitored implicitly:
- SSH latency
- rsync throughput consistency
- Meshnet reachability when remote

Signals:
- Sudden drop below ~100 MB/s on rsync
- Meshnet reachable but LAN not (or vice versa)
- Intermittent SSH stalls

If the network “feels weird,” it probably is.

---

## What is intentionally NOT monitored

- CPU usage graphs
- Memory pressure charts
- Per-process metrics
- Always-on dashboards
- Automated alerting emails

These generate noise and false confidence.
felixOrg prioritizes **clarity over completeness**.

---

## Periodic manual checks (cadence)

### Daily (informal)
- Does RFStudio feel normal?
- Can RFiMac be reached?
- Any unexpected prompts, warnings, or delays?

### Weekly
- Review rsync logs
- Check free space on RFiMac and RFStudio
- Note any recurring warnings

### Monthly
- Disk health review
- Archive integrity spot-check
- Confirm Time Machine backups are current

### Quarterly
- Offsite Time Machine disk rotation
- Review `/etc/hosts` for staleness
- Re-read this file and see if reality drifted

---

## Failure response model

1. **Stop** — don’t “fix” while guessing
2. Identify which tier is affected
3. Check last known-good state
4. Consult documentation (this repo)
5. Make the smallest reversible change

If the fix isn’t reversible, it’s not a fix — it’s a gamble.

---

## Logging and records

There is no central log aggregator.

Instead:
- rsync logs are preserved where relevant
- DEVONthink and Calibre behavior is noted in journals
- Significant incidents are documented in Markdown

Memory is unreliable. Notes are cheap.

---

## Known blind spots (acknowledged risks)

- No early-warning SMART failure alerts
- No automated disk-space alerts
- No centralized alerting if away for long periods

These are accepted risks given:
- Low operational churn
- Human presence
- Conservative change practices

If uptime requirements change, this will be revisited.

---

## Future options (explicitly not implemented yet)

- Lightweight uptime checks for Tier 0/1 hosts
- SMART polling with manual review
- Periodic checksum audits for archives

None are added until they reduce risk without adding cognitive load.

---

## Closing rule

> If monitoring tells you something is “fine”  
> but your experience says it isn’t —  
> believe your experience.

---

## Change log
- 2025-12-18: Initial monitoring operations document synthesized from current felixOrg practices and failure-avoidance philosophy.
```
