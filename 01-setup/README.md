# 01 · Setup — VMware networks, VM shells, pfSense base install

## 1. Assets

| Needed | Status |
|---|---|
| pfSense CE (current) | ✅ `netgate-installer-v1.2-RELEASE-amd64.iso` on hand — this is Netgate's unified installer, not the OS image itself; booting it prompts you to pick an edition. **Pick pfSense CE** (not Plus) when asked. |
| Ubuntu Server LTS ISO | ✅ `ubuntu-26.04.1-live-server-amd64.iso` on hand |
| Kali Linux VMware image | ✅ `kali-linux-2026.2-vmware-amd64` on hand |

## 2. Networking plan (VMware Workstation Pro — LAN Segments)

Using VMware's **LAN Segments** (VM Settings → Network Adapter → Custom → "LAN segment:") instead of named VMnets — functionally the same thing, just scoped per-VM rather than globally in the Virtual Network Editor. pfSense gets **3 adapters**, Kali and Ubuntu get **1 each**, on two separate segments so pfSense genuinely sits between them (not a flat shared network):

| Adapter | On | Connects to | Purpose |
|---|---|---|---|
| pfSense Adapter 1 | pfSense | Bridged | Real internet access, for pfSense package/OS updates only — not part of the attack path |
| pfSense Adapter 2 | pfSense | `LAN Segment` (attacker-facing) | This is pfSense's **WAN** interface for lab purposes — Kali lives here |
| pfSense Adapter 3 | pfSense | `LAN Segment 2` | This is pfSense's **LAN** interface — Ubuntu/DVWA lives here |
| Kali Adapter 1 | Kali | Same `LAN Segment` as pfSense Adapter 2 | Attacker network |
| Ubuntu Adapter 1 | Ubuntu | Same `LAN Segment 2` as pfSense Adapter 3 | Protected network |

```
                         real internet (updates only)
                                │
                          pfSense Adapter 1 (Bridged)
                                │
Kali ──► LAN Segment ──► pfSense Adapter 2 (WAN, lab-facing)
                          pfSense Adapter 3 (LAN) ──► LAN Segment 2 ──► Ubuntu/DVWA
```

Kali and Ubuntu must **never** be on the same LAN Segment — that would put them on a flat network with pfSense not actually positioned between them, which defeats the whole point of this lab (proving pfSense blocks Kali's traffic to Ubuntu).

## 3. pfSense VM

- 2 GB RAM, 8–20 GB disk (matches current build)
- 3 network adapters as above: Adapter 1 → Bridged, Adapter 2 → Custom LAN Segment (attacker side), Adapter 3 → Custom LAN Segment 2 (protected side)
- Boot `netgate-installer-v1.2-RELEASE-amd64.iso`; when prompted for edition, **select pfSense CE**
- At the console, assign interfaces: **WAN = Adapter 2** (the attacker-facing segment, *not* the bridged adapter), **LAN = Adapter 3**
- Accept pfSense defaults for LAN: `192.168.1.1/24` with DHCP enabled

Record here once done:
- pfSense WAN-side IP (on the Kali segment): `___________`
- pfSense LAN IP: `192.168.1.1` (default, DHCP range `192.168.1.100–192.168.1.199`)

## 4. Kali VM

- 2 vCPU, 2 GB RAM, 20 GB disk
- 1 network adapter → Custom, same LAN Segment as pfSense's Adapter 2
- Confirm it gets a DHCP lease from pfSense's WAN-side interface (or set static — note which)

Kali IP: `___________`

## 5. Ubuntu Server VM

- 1 vCPU, 2 GB RAM, 20 GB disk — ✅ install in progress (LVM storage layout confirmed, `/boot` + `ubuntu-vg`)
- 1 network adapter → Custom, same LAN Segment 2 as pfSense's Adapter 3
- Boot `ubuntu-26.04.1-live-server-amd64.iso`, minimal install is fine (DVWA stack goes on in setup step, not here)

Ubuntu IP: `___________` (should land in `192.168.1.100–199` via pfSense's LAN DHCP)

## 6. Sanity check before moving on

- [ ] pfSense web GUI reachable from host (browse to pfSense's LAN IP, `192.168.1.1`, from Kali or via VMware's own network path)
- [ ] Kali can `ping` pfSense's WAN-side IP
- [ ] Ubuntu can `ping` pfSense's LAN IP (`192.168.1.1`)
- [ ] Kali **cannot yet** reach Ubuntu directly (pfSense hasn't been told to allow/route it — confirms the chokepoint is real before you configure anything)

Screenshot the topology (VM Settings → Network Adapter for each machine + `ip a` on all three) into `../screenshots/`.

Next: [`02-firewall-config/`](../02-firewall-config/)
