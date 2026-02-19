# Domain Controller Deployment

## Overview
This document details the installation, configuration, and promotion of the Dell PowerEdge R440 server to a fully functional Active Directory Domain Controller within the LAB.LOCAL homelab environment.

The deployment extends the baseline Windows Server installation into an enterprise-style identity and authentication infrastructure.

---

## 1. Role Installation
Active Directory Domain Services (AD DS) and DNS were installed using Server Manager

### Installation Path
1. Open Server Manager
2. Select Add Roles and Features
3. Choose Role-Based or feature-based installation
4. Select local server from server pool
5. Install:
- Active Directory Domain Services
- DNS Server

All additional feature prompts were accepted with default selections.

## 2. Domain Promotion
After role installation, the server was promoted to a Domain Controller.

### Deployment Configuration
- ***Deployment Model***: Add a new first
- ***Root Domain Name***: LAB.LOCAL (different from actual)
- ***Forrest Functional Level***: Windows Server 2016
- ***Domain Functional Level***: Windows Server 2016
- ***DNS Server***: Enabled
- ***Global Catalog***: Enabled
- ***Read-Only Domain Controller***: Disabled

## 3. DNS Configuration
DNS delegation was not configured

Reason:
- This is a new forest
- No parent DNS zone exists
- Internal-only namespace in lab environment

The Domain Controller hosts DNS services for LAB.LOCAL

## 4. Database & SYSVOL Configuration
Default paths were retained:
- NTDS Database
- Log files
- SYSVOL folder

Rationale:
- Standardized configuration
- Suitable for lab-scale deployment
- No performance constraints requiring separation

## 5. Automation Consideration
Before completing promotion, the generated PowerShell script was exported.

Purpose:
- Enabled repeatable deployments
- Preserve configuration parameters
- Allow future automation

## 6. Post-Promotion Verification
Upon reboot, multiple validation checks were preformed

### 6.1 Domain Controller Discovery
`Get-ADDomainController -Filter *`

Confirmed:
- Correct hostname
- Correct IP address
- Global Catalog enabled
- Domain functional

### 6.2 Active Directory Diagnostics
`dcdiag /v`

Results:
- Connectivity: Passed
- Advertising: Passed
- SysVolCheck: Passed
- NetLogons: Passed
- Replications: Passed
- Services: Passed
- RID Manager: Passed
- VerifyReferences: Passed

## 7. DFS Replication Observation
An initial DFSREvent (Event ID 1202) was observed.

Cause:
- Single-DC topology
- No replication partner available

Impact:
- No functional impairment
- Expected behavior in lab environment

Conclusion:
DFS replication operating as expected for single-domain-controller infrastructure.

## 8. Service Validation
Verified critical services were running:
- NTDS
- DNS
- DFSR
- KDC
-NetLogon
- RPC
- W32Time

SYSVOL and NETLOGON shares were confirmed accessible

## 9. FSMO Role Verification
All FSMO roles assigned to LAB-DC1:
- Schema Master
- Domain Naming Master
- RID Master
- PDC Emulator
- Infrastructure Master

Single-DC environment expected to hold all roles.

## 10. Deployment Outcome
The Dell R440 server is now operating as:
- Active Directory Domain Controller
- DNS Server for LAB.LOCAL
- Global Catalog
- Authentication authority for lab clients

The domain infrastructure is stable and validated
