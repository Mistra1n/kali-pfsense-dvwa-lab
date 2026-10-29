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

### 2026-10-27 — VM shells underway, networking approach revised

- Used VMware **LAN Segments** instead of global VMnets in the Virtual Network Editor (functionally equivalent, just scoped per-VM) — `01-setup/` rewritten to match actual approach used.
- pfSense VM created (named "pfSence" in VMware — just a label typo, harmless) with 3 adapters: Adapter 1 = Bridged, Adapter 2 = LAN Segment, Adapter 3 = LAN Segment 2.
- **Caught before it caused a problem:** initial instinct was to put Kali and Ubuntu on the *same* LAN Segment behind pfSense. That would've put them on a flat shared network with pfSense not actually positioned between them — defeating the entire premise of this lab (pfSense blocking Kali → Ubuntu attacks). Corrected: Kali gets its own segment (acts as pfSense's attacker-facing/WAN side), Ubuntu gets a separate segment (pfSense's LAN side). Bridged adapter is for pfSense's own internet access (updates) only, not part of the attack path.
- Sticking with pfSense's default LAN subnet (`192.168.1.0/24`) rather than a custom scheme — one less thing to misconfigure.
- Ubuntu Server 26.04.1 install in progress — storage config confirmed (LVM: `ubuntu-vg`, 10G `/` + 1.77G `/boot`, default partitioning on a 20G disk).

### 2026-10-29 — Ubuntu login confirmed; two pfSense install hiccups

- **Ubuntu install complete.** Logged in successfully as `mistra1n` on hostname `mist`, Ubuntu 26.04.1 LTS. First login attempt failed with a typo (`mitsra1n`), second attempt with correct username worked. No other issues.
- **pfSense installer error:** "Cannot reach the Netgate Servers, please verify your network settings!" when WAN tried to phone home during install. Expected — WAN sits on the isolated Kali segment with no internet path. Not a real error, clicked past it.
- **VMware Virtual Network Editor error:** "Cannot change network to bridged: There are no un-bridged host network adapters." — tried to set `VMnet8` to Bridged mode and VMware refused. Likely cause: this host's only physical adapter is Wi-Fi, already claimed elsewhere, or Wi-Fi bridging isn't supported cleanly on this hardware. Also noticed `VMnet0` (the usual default bridged network) is missing from the Virtual Network Editor list entirely — `VMnet1`, `VMnet2`, `VMnet3`, `VMnet8` are the only ones present.
- **Decision: dropped the bridged/internet-access adapter on pfSense entirely.** It was only ever meant for pfSense's own package updates, not part of the Kali→pfSense→Ubuntu attack path this lab is testing. pfSense Adapter 1 stays disconnected; only Adapter 2 (WAN/Kali segment) and Adapter 3 (LAN/Ubuntu segment) are in use. `01-setup/` updated to match — one less moving part to troubleshoot.
