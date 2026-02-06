# Dell R440 Homelab

This repository documents the setup and configuration of a Dell PowerEdge R440 server for a homelab environment, focused on cybersecurity and networking labs. The goal was to establish a **stable, enterprise-grade foundation** from a bare-metal deployment to secure management

---

## Key Highlights

- **Hardware**: Dell PowerEdge R440, Intel Xeon Silver 4110, 32 GB RAm, PERC H330 RAID controller, 2x1TB SATA drives in RAID 1
- **OS**: Windows Server 2019 Standard, UEFI boot, Secure Boot enabled
- **Networking**: Shared LOM interface for iDRAC and host traffic, integrated into existing /22 network
- **Management**: iDRAC 9 (Basic), remote desktop access with NLA enforced
- **Security & Hardening**: iDRAC crednetial rotation, encrypted management protocols, firewall configured for administrative access

---

## Key Learning

- **iDRAC Virtual Media Limitations**: Learned that Virtual Media requires an Enterprise license, highlighting the importance of verifying licensing before deployment
- **Out-of-Band Management on Limited Infrastructure**: Configured LOM1 NIC sharing to maintain iDRAC access on a single available port
- **Manual Firmware & Driver Updates**: Automated Dell | Command Update (DCU) failed due to catalog detection issues; manual updates established a verifiable baseline

---

## Repository Structure

```text
dell-r440-homelab/
├── README.md
├── docs/
│   ├── architecture.md         # Hardware overview & network topology
│   ├── deployment.md           # Step-by-step deployment and troubleshooting
│   ├── recovery-decisions.md   # Recovery strategy & decision rationale
│   └── security-notes.md       # Hardening and access control notes
├── screenshots/                # Proof of setup and validation
├── configs/                    # Baseline configurations (network, system)
└── LICENSE

