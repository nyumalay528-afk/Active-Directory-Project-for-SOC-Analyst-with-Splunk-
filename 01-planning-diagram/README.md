# Phase 1: Planning & Logical Diagram

## Objective

Design the lab environment on paper before building it, and define hardware/network requirements.

## What I Did

- Defined lab requirements: 16GB RAM / 250GB disk recommended; noted that Apple Silicon Macs (M1/M2/M3) need a cloud provider due to virtualization limits, while Windows hosts can use VirtualBox directly.
- Built a logical network diagram in [draw.io](https://app.diagrams.net) containing:
  - 2 servers: Active Directory Domain Controller, Splunk server
  - 2 clients: Windows 10 (target), Kali Linux (attacker — color-coded red)
  - 1 switch, 1 router, 1 cloud (internet)
- Connected all endpoints → switch → router → internet.
- Assigned the domain name (`cyberx.local`), network range (`192.168.10.0/24`), and static IPs per machine.
- Mapped software-to-machine: Splunk Universal Forwarder + Sysmon on AD and Target machines; Atomic Red Team on the Target machine.
- Added dotted green lines to represent log-forwarding data flow from endpoints → Splunk.

## Lessons Learned

- Planning IP scheme and log flow *before* installing anything avoided rework later in Phase 3.
- Color-coding by role (attacker vs. defender assets) makes the diagram immediately readable to someone unfamiliar with the lab — useful when presenting this to others.
