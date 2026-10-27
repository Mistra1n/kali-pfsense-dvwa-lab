# Running log — findings & errors

Chronological, dated entries as the lab gets built. The final `README.md` in this folder stays the clean summary; this file is the messy in-progress journal.

---

### 2026-10-27 — Asset check

- Have: Kali Linux 2026.2 VMware image, a pfSense OVA (**version 2.1, from 2014 — too old, replacing**), leftover Wazuh/Win10 files from the other lab (not needed here).
- Missing: Ubuntu Server ISO — needed for the DVWA target VM.
- Decision: download current pfSense CE (2.9.0) instead of using the old OVA. [Source](https://docs.netgate.com/pfsense/en/latest/releases/index.html)
- Hypervisor confirmed as VMware Workstation Pro (not VirtualBox as originally scaffolded) — `01-setup/` rewritten for VMware's Virtual Network Editor / custom VMnets instead of VirtualBox Internal Networks.

### 2026-10-27 — Install media ready

- Downloaded `netgate-installer-v1.2-RELEASE-amd64.iso` for pfSense — this is Netgate's newer unified installer, not a direct OS image. It prompts for edition (CE vs Plus) on first boot; noted in `01-setup/` to select **CE**.
- Downloaded `ubuntu-26.04.1-live-server-amd64.iso` (newer LTS than the 22.04/24.04 originally assumed — docs updated to match).
- All three install sources now on hand: Kali 2026.2 (VMware image), pfSense installer, Ubuntu 26.04.1 Server. Next: create the two isolated VMnets in VMware's Virtual Network Editor and build the VM shells per `01-setup/`.
