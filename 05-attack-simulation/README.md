Phase 5: Attack Simulation & Detection

Objective

Simulate a real attack against the domain, then use Splunk to detect and analyze it — the "blue team" payoff of the whole lab.

Part A: RDP Brute-Force Attack

Setup

Configured Kali Linux with a static IP and updated repos.
Installed Hydra for the brute-force attack.
Built a password list (iii.txt) seeded with the known correct password for the test account.
Enabled RDP on the Windows target and added the test user to the allowed remote-desktop list.

Attack

hydra -l administrator -P iii.txt rdp://192.168.10.100 -V

Ran from the Kali attacker VM against the target machine (192.168.10.100), attempting the administrator account with each password in the list.

Detection (Splunk)

index="endpoint" EventCode=4625 src_ip=192.168.10.250 Account_Name=administrator
| stats count by Account_Name, src_ip
| sort - count

Query confirms 103 failed logon events (EventCode 4625) originating from the Kali attacker's IP (192.168.10.250) against the administrator account — a clear brute-force signature.

If a successful login was captured, add a second query here: index=endpoint EventCode=4624 Account_Name=administrator and screenshot the result to show the attack ultimately succeeded.

Show Image

Part B: Atomic Red Team (ART) Telemetry

Setup

On the Windows target: set PowerShell execution policy to allow scripts, added a Windows Defender exclusion for C:\, and installed the Atomic Red Team framework.

Execution Ran ART tests mapped to MITRE ATT&CK techniques, e.g.:

T1136 — Create a local user account
T1059 — Execute a malicious PowerShell command

Detection (Splunk) Searched index=endpoint filtering on Sysmon EventCode 1 (process creation) and Windows Security EventCode 4720 (user account created) to confirm each ART test generated the expected telemetry.

Cleanup
Took a VirtualBox snapshot of the environment post-attack for easy rollback/re-testing.
