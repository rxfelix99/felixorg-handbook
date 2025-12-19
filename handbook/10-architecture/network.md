# network.md — felixOrg Network (Best-Guess, Working Reality)

This document describes how the felixOrg network actually behaves day-to-day, not how it *should* behave in a vendor brochure. Assumptions are called out explicitly so future-you can correct past-you without archaeology.

---

## Subnets and addressing

### Primary LAN
- **CIDR:** `192.168.50.0/24`
- **Gateway / Router:** `192.168.50.1` (ASUS mesh router acting as both WAN edge and LAN switch)
- **Broadcast:** `192.168.50.255`
- **Netmask:** `255.255.255.0`

This is a flat LAN. No VLANs, no segmentation, no illusions of enterprise hygiene. Everything important lives here.

### Address usage pattern (de facto)
| Range | Usage |
|---|---|
| `.1` | Router / gateway |
| `.50–.59` | Core infrastructure (Intel hosts, critical services) |
| `.60–.69` | Primary desktops / workstations |
| `.70–.89` | Linux hosts, media systems, servers |
| `.200+` | Laptops / transient or legacy systems |

This is **policy by habit**, not enforced by the router. It works because you’re disciplined, not because the network is.

### Known static or quasi-static assignments
| IP | Host |
|---|---|
| `192.168.50.50` | RFiMac |
| `192.168.50.60` | RFStudio |
| `192.168.50.70` | rf-ubuserv |
| `192.168.50.72` | fin11 (VM on RFiMac) |
| `192.168.50.80` | rf-ubudesk |
| `192.168.50.82` | RFMini |
| `192.168.50.200` | RFPro |

Whether these are router-level DHCP reservations or OS-level static configs varies by host. That inconsistency is technical debt, not a feature.

### Mesh / overlay network
- **Provider:** NordVPN Meshnet
- **Address space:** `100.64.0.0/10` (CGNAT-style overlay)
- **Observed examples:**
  - RFStudio: `100.123.195.58`
  - RFiMac: `100.82.81.204`
  - RFMini: `100.76.53.245`
  - RFAirz: `100.80.116.14`

Mesh IPs are **not stable identifiers**. Treat them as session artifacts unless explicitly pinned by Meshnet policy.

---

## DNS / hosts conventions

### Reality check
There is **no internal DNS server**. Name resolution is handled by:
1. `/etc/hosts` (manual, brittle, but fast)
2. Router-provided DNS (DHCP)
3. External DNS (when the above fail)

This is intentional minimalism, not negligence.

### `/etc/hosts` usage
- Used for **core machines only** (RFStudio, RFiMac, key VMs).
- Used to paper over:
  - Mesh IP instability
  - Router DNS flakiness
  - The fact that consumer routers are bad at being infrastructure

Example pattern:
