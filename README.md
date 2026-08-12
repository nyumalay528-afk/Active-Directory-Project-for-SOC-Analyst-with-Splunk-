====================================================================
Active Directory for  SOC Analyst with Splunkss
====================================================================

A self-built cybersecurity lab designed to practice Active Directory administration, centralized log collection, and SOC detection engineering. The environment simulates a small corporate network consisting of a domain controller, Splunk log server, Windows client, and Kali Linux attacker machine. It also includes a controlled attack simulation with corresponding Splunk detections, allowing the full attack and detection process to be investigated in a realistic SOC-style environment.

Based on the MyDFIR "Active Directory Project (Home Lab)" series, rebuilt and documented independently as a portfolio project.

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

  
-------------------------------------------------------------------------------------
OBJECTIVE
-------------------------------------------------------------------------------------

This project focuses on building a functional on-premises domain environment from the ground up and configuring it for centralized security logging. I then simulated a real-world attack technique within the environment and used Splunk to monitor, detect, and investigate the resulting activity. The entire process was documented from a SOC analyst’s perspective, including the attack, detection, investigation, relevant evidence, and final findings.

-------------------------------------------------------------------------------------
ENVIRONMENT / TOOLS
-------------------------------------------------------------------------------------

Component           			Role                        Software


Domain Controller  		 Active Directory, DNS       	Windows Server 2022

Splunk Server       		 Centralized logging (SIEM) 	Ubuntu 22.04 + Splunk 

Target Machine       		Domain-joined client       	Windows 10 Pro

Attacker Machine     		Adversary simulation       	Kali Linux

Hypervisor           		Virtualization               	VirtualBox

Log Collection      		 Endpoint telemetry          	Sysmon (Olaf) +SUF

Attack Simulation   		 Brute-force with Crowbar	Atomic Red Team

Network Design      		 Architecture diagram        	draw.io



Network: 192.168.10.0/24 (isolated NAT network, internal-only)

--------------------------------------------------------------------
PROJECT PHASES
--------------------------------------------------------------------
Phase 1 - Planning & network diagram
          Folder: 01-planning-diagram/

Phase 2 - VM installation (VirtualBox, Windows Server, Windows 10, Kali, Ubuntu)
          Folder: 02-vm-installation/

Phase 3 - Network config, Splunk install, Sysmon deployment
          Folder: 03-network-splunk-sysmon/

Phase 4 - Active Directory install, domain join, users/OUs
          Folder: 04-active-directory-setup/

Phase 5 - Attack simulation (brute force + Atomic Red Team) and
          Splunk detection
          Folder: 05-attack-simulation/


--------------------------------------------------------------------
KEY DETECTION: RDP BRUTE-FORCE ATTACK
--------------------------------------------------------------------
Scenario:
From the Kali Linux machine, ran crowbar against the domain-joined
Windows 10 target over RDP using a curated password list (subset of
rockyou.txt containing the correct password).

Detection query (Splunk SPL):

    index=endpoint EventCode=4625
    | stats count by Account_Name, src_ip
    | where count > 5

Findings:
- Multiple EventCode=4625 (failed logon) events logged in rapid
  succession from the same source, targeting a single domain
  account.
- Followed by a single EventCode=4624 (successful logon) once the
  correct password was reached - the classic brute-force signature.
- Confirms Sysmon + Universal Forwarder pipeline correctly ingests
  Windows Security logs into the "endpoint" index in near real time.


--------------------------------------------------------------------
SKILLS DEMONSTRATED
--------------------------------------------------------------------
- Windows Server / Active Directory Domain Services installation
  and configuration
- OU and user account management
- Linux server administration (Ubuntu)
- SIEM deployment and log source onboarding (Splunk Universal
  Forwarder, Sysmon)
- Network segmentation and static IP configuration in a virtualized
  environment
- Adversary simulation and MITRE ATT&CK-aligned technique testing
  (Atomic Red Team)
- Log analysis and SPL query writing for threat detection
- Technical documentation


--------------------------------------------------------------------
REPO STRUCTURE
--------------------------------------------------------------------

|-- README.txt
|-- 01-planning-diagram/
|-- 02-vm-installation/
|-- 03-network-splunk-sysmon/
|-- 04-active-directory-setup/
|-- 05-attack-simulation/

Each phase folder contains its own README with step-by-step notes
and screenshots for that stage.


--------------------------------------------------------------------
NOTES
--------------------------------------------------------------------
- All IPs, hostnames, and credentials shown are lab-only and used on
  an isolated internal network with no internet-facing exposure.
- VM disk images are not included in this repo (too large for git) -
  only configs, scripts, queries, and screenshots.
