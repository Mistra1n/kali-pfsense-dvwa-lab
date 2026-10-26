# Running log — findings & errors

Chronological, dated entries as the lab gets built. The final `README.md` in this folder stays the clean summary; this file is the messy in-progress journal.

---

### 2026-10-27 — Asset check

- Have: Kali Linux 2026.2 VMware image, a pfSense OVA (**version 2.1, from 2014 — too old, replacing**), leftover Wazuh/Win10 files from the other lab (not needed here).
- Missing: Ubuntu Server ISO — needed for the DVWA target VM.
- Decision: download current pfSense CE (2.9.0) instead of using the old OVA. [Source](https://docs.netgate.com/pfsense/en/latest/releases/index.html)
- Hypervisor confirmed as VMware Workstation Pro (not VirtualBox as originally scaffolded) — `01-setup/` rewritten for VMware's Virtual Network Editor / custom VMnets instead of VirtualBox Internal Networks.
