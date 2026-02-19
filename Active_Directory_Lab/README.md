# Active Directory Domain Deployment - R440 Homelab Extension

This repository documents the deployment and validation of Active Directory Domain Services (AD DS) within the existing Dell PowerEdge R440 homelab environment.

The objective was to extend the bare-metal Windows Server installation into a functional enterprise identity infrastructure, including domain controller promotion, DNS configuration, user provisioning, and Windows 10 client integration.

---

## Key Highlights
- **Domain Controller**: LAB-DC1 (Single-DC topology)
- **Functional Level**: Windows Server 2019
- **DNS**: Integrated with Active Directory
- **Client Integration**: Windows 10 Pro VM joined to domain
- **Authentication Testing**: Verified via whoami, systeminfo, and nltest
- **Health Validation**: dcdiag /v run post-promotion with expected single-DC DFSR behavior

---

## Key Learning
- **DFS Replication Behavior in Single-DC Environments**:
Observed DFSREvent (Event 1202) during initial diagnostics. The determined root cause was absence of a replication partner. Confirmed no functional impact.
- **DNS as a Dependency for Domain Join**:
Verified that incorrect DNS configuration prevents successful domain join, reinforcing the importance of directing to the DC for name resolutions.
- **Active Directory Health Validation**:
Used dcdiag, Get-ADDomainController, and nltest, to confirm proper DC advertising, SYSVOL readiness, FSMO role assignment, and client authentication.
- **Static Client Configuration in Controlled Labs**:
Configured manual IP and DNS settings to ensure deterministic connectivity during domain integration testing.

---

## Repository Structure

```text
Active_Directory_Lab/
├── README.md
├── docs/
│ ├── dc-deployment.md # AD DS installation & promotion
│ ├── dcdiag_results.txt # dcdiag analysis & diagnostics
│ ├── Users_Created.md # User creation steps & validation
│ └── vm-join-instructions.md # Windows 10 domain join guide
├── screenshots/ # Validation proof & command output

