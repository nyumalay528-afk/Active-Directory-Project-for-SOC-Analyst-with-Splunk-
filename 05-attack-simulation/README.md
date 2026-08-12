# Phase 5: Attack Simulation & Detection

## Objective
Simulate a real attack against the domain, then use Splunk to detect and analyze it — the "blue team" payoff of the whole lab.

## Part A: RDP Brute-Force Attack

**Setup**
- Configured Kali Linux with a static IP and updated repos.
- Installed the `crowbar` brute-force tool.
- Built a password list (subset of `rockyou.txt`, seeded with the known correct password for the test account).
- Enabled RDP on the Windows target and added the test user to the allowed remote-desktop list.

**Attack**
```bash
crowbar -b rdp -s 192.168.10.100/32 -u <target_user> -C passwords.txt
```

**Detection (Splunk)**
```spl
index=endpoint EventCode=4625
| stats count by Account_Name, src_ip
| where count > 5
```
- Confirmed a burst of `EventCode=4625` (failed logon) events from the Kali IP against the target account.
- Followed by exactly one `EventCode=4624` (successful logon) once crowbar hit the correct password — the classic brute-force signature.

## Part B: Atomic Red Team (ART) Telemetry

**Setup**
- On the Windows target: set PowerShell execution policy to allow scripts, added a Windows Defender exclusion for `C:\`, and installed the Atomic Red Team framework.

**Execution**
- Ran ART tests mapped to MITRE ATT&CK techniques, e.g.:
  - T1136 — Create a local user account
  - T1059 — Execute a malicious PowerShell command

**Detection (Splunk)**
- Searched `index=endpoint` filtering on Sysmon EventCode 1 (process creation) and Windows Security EventCode 4720 (user account created) to confirm each ART test generated the expected telemetry.

## Cleanup
- Took a VirtualBox snapshot of the environment post-attack for easy rollback/re-testing.


