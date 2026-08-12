# Phase 4: Active Directory Installation & Domain Join

## Objective

Promote the Windows Server to a Domain Controller and join the Windows 10 client to the new domain.

## What I Did

- Assigned a static IP (`192.168.10.7`) to the Windows Server and confirmed connectivity to both internal hosts and the internet.
- Installed the **AD DS (Active Directory Domain Services)** role via Server Manager.
- Promoted the server to a Domain Controller, creating a new forest: `cyberx.local`.
- After reboot, created Organizational Units for departments (**IT**, **HR**) and added test user accounts (e.g., Jenny Smith, Terry Smith).
- Updated the Windows 10 client's DNS settings to point to the Domain Controller.
- Joined the Windows 10 PC to `cyberx.local` using domain admin credentials.
- Logged into the Windows 10 client with a domain user account to confirm the join succeeded.

## Lessons Learned

- Setting DNS on the client to the DC's IP *before* attempting the domain join is the step that trips people up most — domain join fails silently if DNS isn't pointed correctly first.
- Structuring OUs by department early makes it trivial to apply targeted Group Policy later if the lab is extended.

