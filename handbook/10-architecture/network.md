```markdown
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
```

192.168.50.60   rfstudio
100.123.195.58  rfstudio-mesh

```

Rules:
- Lowercase aliases only.
- One alias per line. No cleverness.
- If an entry looks stale, it probably is.

Duplicate or conflicting entries (e.g., multiple `rfimac-mesh` lines) are a **bug**, not a mystery. Fix them.

### Naming conventions
- **Physical hosts:** `rfstudio`, `rfimac`, `rfmini`
- **Linux systems:** `rf-ubu*`
- **VMs:** named explicitly and treated as first-class citizens (`fin11`)
- **Mesh aliases:** `*-mesh` suffix, nothing fancy

If a name is ambiguous, it’s wrong.

---

## Remote access (Mesh / VPN)

### Primary remote access method
- **NordVPN Meshnet**
- Used for:
  - SSH
  - Screen sharing / RDP
  - Emergency access when LAN routing is unavailable

Meshnet is the *default* remote access path, not a fallback.

### Secondary / implicit methods
- **SSH over LAN** when local
- **Screen Sharing / ARD** within LAN
- No exposed inbound ports on the WAN edge
- No consumer-grade port forwarding hacks

This is conservative by design.

### Security posture (practical, not performative)
- Authentication is host-level (SSH keys, OS accounts).
- Meshnet provides transport reachability, not trust.
- Sensitive workloads (finance, Quicken):
  - Run only on `fin11`
  - Hosted only on RFiMac
  - Accessed deliberately, not casually

There is no assumption that “inside the LAN” equals “safe.”

### Future considerations (explicitly deferred)
- Tailscale vs Cloudflare Tunnel: already evaluated conceptually, not yet implemented.
- Zero-trust identity overlays: possible, not urgent.
- Internal DNS (Unbound / Pi-hole): nice to have, not worth the maintenance today.

---

## Traffic flow (important, non-obvious)

### Backup traffic
- **Method:** `rsync over ssh`
- **Typical throughput:** ~100–110 MB/s
- **Bottleneck:** 1 GbE LAN, not disk I/O
- **Router involvement:** Yes — traffic hairpins through the ASUS router even for host-to-host LAN transfers

This explains why a 10 GbE upgrade was correctly judged “not worth it (yet).”

### VM networking
- VMs are bridged into `192.168.50.0/24`
- VMs are addressable peers, not NAT-hidden pets
- This simplifies backup, SSH, and monitoring at the cost of some isolation

That tradeoff is deliberate.

---

## Diagram references
- **Source:** `assets/diagrams/network_topology.vsdx`
- **Exports:**
  - `assets/diagrams/network_topology.pdf`
  - `assets/diagrams/network_topology.svg`

The diagram should reflect:
- Flat LAN topology
- Router as both WAN edge and internal switch
- Mesh overlay as a parallel plane, not “the network”
- Explicit VM placement (especially `fin11` on RFiMac)

If the diagram disagrees with this file, the diagram is lying.

---

## Known weaknesses (write them down so they don’t bite later)
- Flat LAN = lateral movement is trivial.
- `/etc/hosts` does not scale.
- Consumer router doing too many jobs.
- No authoritative internal DNS.
- Mesh IP churn causes confusion if not documented.

All known, all accepted — until requirements change.

---

## Change log
- 2025-12-18: Initial populated version based on observed addressing, `/etc/hosts`, rsync behavior, and current felixOrg operating practices.
```
# Network

## Subnets and addressing

## DNS / hosts conventions

## Remote access (Mesh/VPN)

## Diagram references
- Source: `assets/diagrams/network_topology.vsdx`
- Exports: `assets/diagrams/network_topology.pdf`, `assets/diagrams/network_topology.svg`
