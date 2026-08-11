# Phase 3: Network Configuration & Splunk/Sysmon Deployment

## Objective

Isolate the lab on its own virtual network, deploy Splunk as the central log server, and instrument endpoints with Sysmon + the Universal Forwarder.

### Network

- Created a VirtualBox NAT Network named `AD Project` (`192.168.10.0/24`) and attached all four VMs to it for internal communication + shared internet access.

### Splunk Server (Ubuntu VM — 192.168.10.10)

- Set a static IP.
- Installed VirtualBox Guest Additions to share the Splunk `.deb` installer from the host.
- Installed Splunk Enterprise, created the admin account, enabled boot-start.

### Windows Target Machine

- Renamed host to `target-pc`, set static IP `192.168.10.100`.
- Installed & configured Splunk Universal Forwarder → pointed at `192.168.10.10:9997`.
- Installed Sysmon using Olaf Hartong's modular config via PowerShell.
- Created `inputs.conf` to forward Application, Security, System, and Sysmon event logs into a new Splunk index called `endpoint`.
- Set the forwarder service to run as Local System and restarted it.

### Splunk Web Configuration
- Created the `endpoint` index.
- Enabled a receiving port on 9997.
- Verified ingestion with `index=endpoint` — confirmed logs arriving from `target-pc`.

### Windows Server (ADDC01)

- Repeated the Sysmon + Universal Forwarder setup, renamed host to `ADDC01`, verified ingestion in Splunk.


## Lessons Learned

- This is the phase that actually makes the lab a *SOC* lab rather than just an IT lab — without reliable log forwarding, none of the Phase 5 detections would be possible.
- Verifying ingestion with a simple `index=endpoint` search immediately after each config change caught a forwarder misconfiguration early rather than debugging it later during the attack simulation.

