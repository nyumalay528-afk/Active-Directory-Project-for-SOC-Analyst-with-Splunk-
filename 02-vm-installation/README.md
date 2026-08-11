# Phase 2: Virtual Machine Installation

## Objective

Stand up all four VMs in VirtualBox to match the Phase 1 diagram.

## What I Did

- Installed VirtualBox and required dependencies on the host.
- **Windows 10 Pro (Target):** downloaded ISO, created VM (4GB RAM / 1 CPU / 50GB disk), installed OS.
- **Kali Linux (Attacker):** imported pre-built Kali VM image, logged in with default `kali/kali` credentials.
- **Windows Server 2022 (future Domain Controller):** downloaded ISO, created VM (4GB RAM / 50GB disk), installed "Standard Edition (Desktop Experience)," set local administrator password.
- **Ubuntu Server 22.04 (future Splunk server):** created VM (8GB RAM / 2 CPU / 100GB disk), installed OS, created a local user, ran `apt update && apt upgrade`.

## Lessons Learned

- Allocating enough RAM/CPU to the Splunk VM up front (8GB/2CPU) avoided indexing slowdowns later once log volume increased in Phase 3–5.
- Keeping VM naming consistent with the Phase 1 diagram (target-pc, ADDC01, etc.) made later Splunk log correlation much easier to read.

