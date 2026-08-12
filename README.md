# Active Directory & SOC Home Lab

A self-built enterprise-style lab used to practice Active Directory administration, log collection, and SOC detection engineering. The environment simulates a small corporate network — a domain controller, a Splunk log server, a Windows client, and a Kali Linux "attacker" machine — and includes a real attack simulation with matching Splunk detections.

> Based on the MyDFIR "Active Directory Project (Home Lab)" series, rebuilt and documented independently as a portfolio project.

+---------------------------------------------------------------+
|                     Domain: CyberX                            |
|                 Network: 192.168.10.0/24                     |
+---------------------------------------------------------------+

                           Internet
                              |
                              |
                           Router
                              |
                              |
                           Switch
                    _________|_________
                   |         |         |
                   |         |         |
                   |         |         |
            Splunk Server  Windows 10  Kali Linux
            192.168.10.10   IP: DHCP   192.168.10.250
                   |          |          |
                   |          |          |
                   |          |          |
                   |     Splunk Universal |
                   |       Forwarder      |
                   |       Atomic Red Team|
                   |                     |
                   |                     |
                   |                     |
                   |               [Attacker]
                   |
                   |
            Active Directory
             192.168.10.7
                   |
                   |
            Splunk Universal
                Forwarder
                   |
                 Sysmon

## Objective

Build a functional on-prem domain environment from scratch, instrument it for centralized logging, simulate a real attack technique against it, and detect that attack using Splunk — then document the full process the way a SOC analyst would document an investigation.

## Environment / Tools

| Component                  | Role                           | Software |

| Domain Controller          | Active Directory, DNS          | Windows Server 2022 |
| Splunk Server              | Centralized logging (SIEM)     | Ubuntu Server 22.04 + Splunk |
| Target Machine             | Domain-joined client           | Windows 10 Pro |
| Attacker Machine           | Adversary simulation           | Kali Linux |
| Hypervisor                 | Virtualization                 | VirtualBox |
| Log Collection             | Endpoint telemetry | Sysmon(Olaf config) + Splunk Universal Forwarder |
| Attack Simulation           | Brute-force                   | Crowbar, Atomic Red Team |
| Network Design             | Architecture diagram           | draw.io |

**Network:** `192.168.10.0/24` (isolated NAT network, internal-only)

## Project Phases

| Phase | Focus | Link |
|---|---|---|
| 1 | Planning & network diagram | [01-planning-diagram](01-planning-diagram/) |
| 2 | VM installation (VirtualBox, Windows Server, Windows 10, Kali, Ubuntu) | [02-vm-installation](02-vm-installation/) |
| 3 | Network config, Splunk install, Sysmon deployment | [03-network-splunk-sysmon](03-network-splunk-sysmon/) |
| 4 | Active Directory install, domain join, users/OUs | [04-active-directory-setup](04-active-directory-setup/) |
| 5 | Attack simulation (brute force + Atomic Red Team) and Splunk detection | [05-attack-simulation](05-attack-simulation/) |

## Key Detection: RDP Brute-Force Attack

**Scenario:** From the Kali Linux machine, ran `crowbar` against the domain-joined Windows 10 target over RDP using a curated password list (subset of rockyou.txt containing the correct password).

**Detection query (Splunk SPL):**
```spl
index=endpoint EventCode=4625
| stats count by Account_Name, src_ip
| where count > 5
```

**Findings:**
- Multiple `EventCode=4625` (failed logon) events logged in rapid succession from the same source, targeting a single domain account.
- Followed by a single `EventCode=4624` (successful logon) once the correct password was reached — the classic brute-force signature.
- Confirms Sysmon + Universal Forwarder pipeline correctly ingests Windows Security logs into the `endpoint` index in near real time.

**Analyst takeaway:** This pattern (high-frequency 4625 → 4624 from one source/account) is a textbook trigger for an account lockout policy and/or a correlation-rule alert (e.g., "5+ failed logons within 2 minutes followed by success"). See [05-attack-simulation](05-attack-simulation/) for full screenshots and the ART telemetry follow-up.

## Skills Demonstrated

- Windows Server / Active Directory Domain Services installation and configuration
- OU and user account management
- Linux server administration (Ubuntu)
- SIEM deployment and log source onboarding (Splunk Universal Forwarder, Sysmon)
- Network segmentation and static IP configuration in a virtualized environment
- Adversary simulation and MITRE ATT&CK-aligned technique testing (Atomic Red Team)
- Log analysis and SPL query writing for threat detection
- Technical documentation

## Repo Structure

```
.
├── README.md
├── 01-planning-diagram/
├── 02-vm-installation/
├── 03-network-splunk-sysmon/
├── 04-active-directory-setup/
└── 05-attack-simulation/
```

Each phase folder contains its own README with step-by-step notes and screenshots for that stage.

## Notes

- All IPs, hostnames, and credentials shown are lab-only and used on an isolated internal network with no internet-facing exposure.
- VM disk images are not included in this repo (too large for git) — only configs, scripts, queries, and screenshots.
