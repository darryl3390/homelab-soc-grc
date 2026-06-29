# Homelab Build Log
**Owner:** Darryl Briggs  
**Goal:** Build a functioning enterprise-style network to develop hands-on cybersecurity and IT skills in support of CompTIA certifications and a career transition into GRC and cybersecurity engineering.  
**Host:** iMac 2017 — Intel Core i7 — 48GB RAM — VirtualBox  
**Domain:** homelab.local  

---

## Entry 001 — Lab Planning & Platform Decision
**Date:** March 2026  
**Status:** ✅ Complete

**Decision:** Build a dedicated home lab environment to support hands-on learning for CompTIA A+, Network+, Security+, and cybersecurity engineering skills.

**Goals established:**
- Build a safe isolated environment to practice offensive and defensive security techniques
- Replicate a real enterprise network with domain authentication and managed workstations
- Support hands-on study for CompTIA certifications
- Develop skills applicable to SOC analyst and GRC analyst roles

**Initial platform:** MacBook Pro M1 Max (32GB RAM) running UTM hypervisor

---

## Entry 002 — Initial Lab Build on M1 Max (Deprecated)
**Date:** March–April 2026  
**Status:** ⚠️ Deprecated — Migrated to iMac

**Platform:** MacBook Pro M1 Max · 32GB RAM · UTM Hypervisor

**VMs built:**
- Kali Linux (ARM64 — native)
- Ubuntu Server (ARM64 — native)
- Ubuntu Desktop (ARM64 — native)
- Windows Server 2025 (x86 — QEMU emulation)
- Windows 11 Pro (x86 — QEMU emulation)

**Problem encountered:** Windows Server 2025 and Windows 11 Pro required x86 emulation via QEMU on Apple Silicon. This caused severe performance degradation — Active Directory Domain Services installation took several hours and rendered both machines nearly unusable for lab work.

**Resolution:** Decision made to migrate entire lab to a dedicated Intel x86 host where all VMs run natively without emulation overhead.

**Lesson learned:** Match your hypervisor platform to your VM architecture. ARM hosts running x86 emulation are not suitable for Windows Server workloads in a lab environment.

---

## Entry 003 — Platform Migration to iMac 2017
**Date:** May 2026  
**Status:** ✅ Complete

**Action:** Decommissioned the UTM-based lab on the M1 Max. Rebuilt the lab from scratch on a dedicated iMac 2017 running VirtualBox.

**New host specs:**
- CPU: Intel Core i7
- RAM: 48GB
- Hypervisor: VirtualBox (free, well-documented, widely used in professional environments)
- Network mode: Host-Only Adapter — all VMs fully isolated from the internet and physical home network

**VMs installed:**
- Kali Linux
- Windows Server 2025
- Windows 11 Pro

**Network configuration:** All VMs assigned static IP addresses on the VirtualBox host-only adapter for reliable inter-VM communication.

**Rationale for iMac:** Intel i7 architecture runs all VMs natively with zero emulation overhead. 48GB RAM supports five or more simultaneous VMs comfortably.

---

## Entry 004 — Network Connectivity Troubleshooting
**Date:** May 2026  
**Status:** ✅ Resolved

**Problem:** After assigning static IP addresses via the graphical interface on Kali Linux, the changes did not reflect when checking configuration from the command line using `ip a`.

**Root cause:** NetworkManager had not applied the new configuration. The GUI saves settings but the active network connection requires a service restart before new addresses take effect.

**Resolution:**
```bash
sudo systemctl restart NetworkManager
```

**Verified with:**
```bash
ip a
ping [Windows Server IP]
```

**Lesson learned:** On Linux systems using NetworkManager, GUI changes require a service restart or interface bounce to take effect. Always verify with `ip a` after making network changes, not just the GUI display.

---

## Entry 005 — Windows Firewall ICMP Configuration
**Date:** May 2026  
**Status:** ✅ Resolved

**Problem:** Kali Linux could not ping Windows Server 2025 or Windows 11 Pro despite all VMs being on the same host-only subnet with correct IP assignments.

**Root cause:** Windows blocks incoming ICMP (ping) requests by default via Windows Defender Firewall.

**Resolution:** Created an inbound firewall rule to allow ICMPv4 echo requests:
```cmd
netsh advfirewall firewall add rule name="Allow ICMPv4" protocol=icmpv4:8,any dir=in action=allow
```

**Verified:** Successful ping responses from Kali to both Windows VMs.

**Lesson learned:** Windows Firewall blocks ICMP by default — a common gotcha in lab environments. In production, ICMP blocking is intentional security policy. In a lab, it needs to be explicitly permitted for connectivity testing.

---

## Entry 006 — Active Directory Domain Services Installation
**Date:** May–June 2026  
**Status:** ✅ Complete

**Action:** Installed the Active Directory Domain Services (AD DS) role on Windows Server 2025 and promoted the server to Domain Controller.

**Steps executed:**
1. Set static IP on Windows Server 2025
2. Renamed server to DC01 before promotion
3. Installed AD DS role via Server Manager → Add Roles and Features
4. Promoted server to Domain Controller — created new forest with root domain `homelab.local`
5. Set Directory Services Restore Mode (DSRM) password
6. Server rebooted automatically after promotion

**Verified:**
```powershell
Get-ADDomain
Get-ADForest
```

**Outcome:** Domain Controller operational. DNS integrated with AD. `homelab.local` domain active.

**Note:** AD DS installation on the iMac completed in normal time — a stark contrast to the multi-hour installation under QEMU emulation on the M1 Max. Native x86 confirmed as the correct platform decision.

---

## Entry 007 — Windows 11 Domain Join
**Date:** June 2026  
**Status:** ✅ Complete

**Action:** Joined Windows 11 Pro workstation to the `homelab.local` domain managed by DC01.

**Steps executed:**
1. Set DNS on Windows 11 to point to DC01's IP address
2. Verified connectivity: `nslookup homelab.local` returned DC01's IP
3. Joined domain via Settings → System → About → Domain or workgroup → Join a domain
4. Entered domain admin credentials when prompted
5. Rebooted Windows 11
6. Logged in with domain credentials

**Verified on DC01:**
```powershell
Get-ADComputer -Filter * | Select Name
```
Windows 11 workstation appeared in Active Directory Computers container.

**Outcome:** Windows 11 Pro successfully domain-joined. Domain authentication working.

---

## Entry 008 — Active Directory User Creation
**Date:** June 2026  
**Status:** ✅ Complete

**Action:** Created Organizational Units and user accounts in Active Directory to simulate an enterprise user directory.

**Method 1 — PowerShell:**
```powershell
New-ADOrganizationalUnit -Name "Users" -Path "DC=homelab,DC=local"

New-ADUser `
  -Name "John Doe" `
  -GivenName "John" `
  -Surname "Doe" `
  -SamAccountName "jdoe" `
  -UserPrincipalName "jdoe@homelab.local" `
  -Path "OU=Users,DC=homelab,DC=local" `
  -AccountPassword (ConvertTo-SecureString "Password123!" -AsPlainText -Force) `
  -Enabled $true
```

**Method 2 — GUI (Active Directory Users and Computers):**
- Opened ADUC via Windows Administrative Tools
- Created OU via right-click → New → Organizational Unit
- Created user via right-click → New → User
- Set password, unchecked "must change at next logon"

**Verified:** Domain login from Windows 11 using `HOMELAB\jdoe` credentials successful.

**Outcome:** AD user creation via both PowerShell and GUI confirmed working. OU structure established.

---

## Entry 009 — Domain Controller Naming Error and Full Rebuild
**Date:** June 6, 2026  
**Status:** ✅ Resolved

**Problem:** After Active Directory was fully operational, the Windows Server 2025 VM was renamed. This broke Active Directory services and rendered the domain controller non-functional.

**Root cause:** Domain Controller names are embedded in Active Directory during the promotion process. The server's name is registered in AD DNS, Kerberos authentication tickets, replication metadata, and SYSVOL configuration. Renaming a DC after promotion causes cascading failures across all of these systems that are difficult to cleanly resolve — particularly in a single-DC lab environment with no replication partner to recover from.

**Attempted resolution:** Troubleshooting the broken AD state.

**Final resolution:** Made the decision to perform a full reinstall rather than continue troubleshooting a broken configuration. Steps taken:
1. Uninstalled Active Directory Domain Services role
2. Demoted the domain controller
3. Performed clean reinstall of Windows Server 2025
4. Named the server correctly before any role installation
5. Reinstalled AD DS role
6. Repromoted to Domain Controller with domain `homelab.local`
7. Reconfigured DNS and verified domain functionality

**Outcome:** Clean Active Directory installation confirmed. Domain `homelab.local` operational. DNS configured. Ready to rejoin Windows 11 workstation to domain.

**Lesson learned:** In Active Directory environments, the server name is a foundational identity — not a display label that can be changed freely. Always name your domain controller correctly before promotion. In production enterprise environments, renaming a DC is a carefully managed multi-step process documented by Microsoft. In a lab, knowing when to rebuild cleanly rather than chase a broken state is itself a valuable operational judgment call. Document the failure, understand why it happened, and move forward.

**Real-world relevance:** Change management exists in enterprise IT for exactly this reason. Configuration changes to critical infrastructure — especially identity systems like Active Directory — require planning, documentation, and a rollback plan before execution.

---

## Entry 010 — First Vulnerability Scan with Nessus Essentials

**Date:** June 27, 2026
**Status:** ✅ Complete

**Action:** Installed Nessus Essentials on the Windows 11 Pro VM and ran a vulnerability scan against the homelab.local network subnet. OpenVAS installation also in progress for comparison between the two scanners.

**Steps executed:**

- Installed Nessus Essentials on the Windows 11 Pro VM
- Configured a basic network scan targeting the VirtualBox host-only subnet
- Ran the scan and reviewed results in the Nessus dashboard

**Findings:**

- Windows 11 Pro was the only host that returned as active on the scanned network — other VMs (DC01, Kali) were not powered on at the time of the scan
- One vulnerability identified on the Windows 11 Pro VM:
  - **Self-signed X.509 certificate** — a certificate presented by the Nessus web interface running on the Windows 11 VM cannot be trusted because it is self-signed rather than issued by a recognized Certificate Authority
  - **CVSS 3.0 Base Score:** 6.5 (Medium)
  - **Recommended remediation per Nessus:** purchase or generate a proper SSL certificate from a trusted CA

**Verified:**

- Reviewed finding detail and CVSS breakdown in Nessus dashboard
- Confirmed the flagged service was the Nessus web interface itself, not a separate exposed service

**Outcome:** First successful vulnerability scan completed and documented. Confirmed ability to install, configure, and interpret results from an industry-standard vulnerability scanner. CVSS scoring and remediation guidance reviewed and understood in the context of a real, if minor, finding.

**Lesson learned:** A "clean-looking" scan with only one low-to-medium finding is still a valuable exercise — vulnerability management isn't only about finding critical flaws, it's about being able to read, score, and contextualize every finding a scanner returns, including ones related to the scanner's own service. Self-signed certificates are flagged because there's no trusted third party vouching for the identity of the service presenting them, which opens the door to man-in-the-middle attacks.

**Real-world relevance:** Production environments avoid this exact finding by issuing certificates through an internal or public Certificate Authority rather than relying on self-signed certs for anything beyond local testing. Next step is to power on the full VM network (DC01, Kali, Windows 11) during a scan to get a complete picture of the lab's exposed surface, and to run a parallel scan with OpenVAS once installation is complete for comparison between the two platforms.

---


*Log continues as the lab grows. Every new configuration, exercise, troubleshooting event, and rebuild is documented here.*
