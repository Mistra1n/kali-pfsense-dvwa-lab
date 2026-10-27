# 01 · Setup — VMware networks, VM shells, pfSense base install

## 1. Assets

| Needed | Status |
|---|---|
| pfSense CE (current) | ✅ `netgate-installer-v1.2-RELEASE-amd64.iso` on hand — this is Netgate's unified installer, not the OS image itself; booting it prompts you to pick an edition. **Pick pfSense CE** (not Plus) when asked. |
| Ubuntu Server LTS ISO | ✅ `ubuntu-26.04.1-live-server-amd64.iso` on hand |
| Kali Linux VMware image | ✅ `kali-linux-2026.2-vmware-amd64` on hand |

## 2. Networking plan (VMware Workstation Pro — Virtual Network Editor)

Open **Edit → Virtual Network Editor** (run as administrator) and add two custom host-only networks — nothing bridged to the real LAN or internet:

| VMnet | Type | Subnet (example) | Purpose |
|---|---|---|---|
| `VMnet2` | Host-only, no DHCP (pfSense will hand out LAN DHCP itself later) | `192.168.56.0/24` | Kali ↔ pfSense WAN interface |
| `VMnet3` | Host-only, no DHCP | `192.168.57.0/24` | pfSense LAN interface ↔ Ubuntu |

```
Kali NIC1 ───► VMnet2 ───► pfSense NIC1 (WAN)
                            pfSense NIC2 (LAN) ───► VMnet3 ───► Ubuntu NIC1
```

Leave VMware's default NAT (`VMnet8`) alone — don't attach it to any of these three VMs, so the lab stays fully isolated.

## 3. pfSense VM

- 1 vCPU, 2 GB RAM, 8–20 GB disk
- 2 network adapters: NIC1 → Custom (`VMnet2`), NIC2 → Custom (`VMnet3`)
- Boot `netgate-installer-v1.2-RELEASE-amd64.iso`; when prompted for edition, **select pfSense CE**
- Accept defaults through install, assign WAN = first interface / LAN = second interface at the console

Record here once done:
- pfSense WAN IP: `___________`
- pfSense LAN IP: `___________` (default `192.168.1.1`, fine to keep or re-IP to match `VMnet3`'s subnet)

## 4. Kali VM

- 2 vCPU, 2 GB RAM, 20 GB disk
- 1 network adapter → Custom (`VMnet2`)
- Confirm it gets a DHCP lease from pfSense's WAN side (or set static — note which)

Kali IP: `___________`

## 5. Ubuntu Server VM

- 1 vCPU, 2 GB RAM, 20 GB disk
- 1 network adapter → Custom (`VMnet3`)
- Boot `ubuntu-26.04.1-live-server-amd64.iso`, minimal install is fine (DVWA stack goes on in setup step, not here)

Ubuntu IP: `___________`

## 6. Sanity check before moving on

- [ ] pfSense web GUI reachable from host (VMware Workstation can usually reach a host-only network directly from the host machine — browse to pfSense's LAN IP)
- [ ] Kali can `ping` pfSense's WAN IP
- [ ] Ubuntu can `ping` pfSense's LAN IP
- [ ] Kali **cannot yet** reach Ubuntu directly (pfSense hasn't been told to allow/route it — confirms the chokepoint is real before you configure anything)

Screenshot the topology (Virtual Network Editor + `ip a` on all three) into `../screenshots/`.

Next: [`02-firewall-config/`](../02-firewall-config/)
