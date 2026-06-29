# Cybersecurity & IT Home Lab

**Built by:** Darryl Briggs
**Platform:** iMac 2017 · Intel Core i7 · 48GB RAM · VirtualBox
**Goal:** Build a fully functional practice environment that replicates real enterprise IT and security infrastructure — hands-on skill-building in support of a career transition into IT and cybersecurity.

---

## Why This Lab Exists

I'm transitioning into IT and cybersecurity from a background in operational leadership and project management. Certifications prove knowledge — this lab proves application. Every VM, configuration, and exercise here is something I built, broke, troubleshot, and documented myself.

---

## Certifications

| Cert | Status |
|---|---|
| CompTIA A+ | ✅ Complete |
| CompTIA Network+ | ✅ Complete |
| CompTIA Security+ | 🔲 In Progress |

---

## Lab Architecture

| VM | Role | OS |
|---|---|---|
| DC01 | Domain Controller, DNS, DHCP | Windows Server 2025 |
| WIN11 | Domain-joined workstation | Windows 11 Pro |
| Ubuntu Server | Services / practice target (Apache, DVWA) | Ubuntu Server |
| Ubuntu Desktop | Linux workstation / secondary practice target | Ubuntu Desktop |
| Kali Linux | Security tooling and practice platform | Kali Linux |

All VMs run on a VirtualBox host-only network, fully isolated from the internet and physical home network.

**Domain:** `homelab.local`

---

## Tools In Use / Planned

- **Active Directory Domain Services** — identity and access management
- **Wazuh** — SIEM and log monitoring *(planned)*
- **Eramba** — open source GRC platform *(planned)*
- **Nessus Essentials / OpenVAS** — vulnerability scanning *(planned)*
- **DVWA** — intentionally vulnerable web app for practice and learning *(planned)*

---

## Repo Structure
