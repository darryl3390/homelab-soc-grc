# SOC / GRC Analyst Home Lab

**Built by:** Darryl Briggs
**Platform:** iMac 2017 · Intel Core i7 · 48GB RAM · VirtualBox
**Goal:** Build a fully functional analyst practice environment that replicates real enterprise security infrastructure — used to develop hands-on skills in support of a career transition into GRC and cybersecurity engineering.

---

## Why This Lab Exists

I'm transitioning into cybersecurity from a background in operational leadership and project management. Certifications prove knowledge — this lab proves application. Every VM, configuration, and exercise here is something I built, broke, troubleshot, and documented myself.

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
| Ubuntu Server | Services / attack target (Apache, DVWA) | Ubuntu Server |
| Ubuntu Desktop | Linux workstation / secondary target | Ubuntu Desktop |
| Kali Linux | Offensive security platform | Kali Linux |

All VMs run on a VirtualBox host-only network, fully isolated from the internet and physical home network.

**Domain:** `homelab.local`

---

## Tools In Use / Planned

- **Active Directory Domain Services** — identity and access management
- **Wazuh** — SIEM and compliance monitoring *(planned)*
- **Eramba** — open source GRC platform *(planned)*
- **Nessus Essentials / OpenVAS** — vulnerability scanning *(planned)*
- **DVWA** — intentionally vulnerable web app for offensive practice *(planned)*

---

## Repo Structure

```
homelab-soc-grc/
├── README.md
├── build-log.md          → Full history of the lab — what was built, what broke, what was learned
├── exercise-logs/         → Individual hands-on exercises, dated and documented
├── grc-artifacts/         → Risk registers, framework mappings, policy drafts, assessment reports
└── screenshots/           → Visual proof of working systems
```

---

## Build Log

The full build history — including the mistakes — is documented in [`build-log.md`](./build-log.md). It includes a platform migration from Apple Silicon to Intel, network troubleshooting, Active Directory setup, and a full domain controller rebuild after a naming error.

---

## Connect

[LinkedIn](www.linkedin.com/in/darryl-briggs-mba-pmp-5893b5139
) — open to entry-level SOC Analyst and GRC Analyst roles in the NYC metro area.
