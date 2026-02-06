# Dell PowerEdge R440 Bare-Metal Deployment

## Project Overview
This project documents the bare-metal setup of a Dell PowerEdge R440 server. The goal was to deploy a stable, enterprise-grade foundation for future cybersecurity and networking labs

---
## Key Learning
### 1. iDRAC Virtual Media Limitation (Licensing Constraint)
- ***Challenge***: After a failed Rufus-prepared USB, I attempted to mount an ISO remotely via the iDRAC Virtual Media
- ***Learning***: Virtual Media and Remote Console functionality requires an iDRAC Enterprise License. The server had a Basic/Express license, so the feature was locked
- ***Action***: Always verify iDRAC licensing before choosing a remote deployment method
### 2. Out-of-Band (OOB) Resource Management
- ***Challenge***: Limited physical infrastructure (a single router port) required sharing the iDRAC management interface with the host NIC
- ***Action***: Configured NIC Teaming/Sharing within iDRAC, switching from “Dedicated” to LOM1 (Shared) to carry both iDRAC and host OS traffic over one cable
### 3. Austomated Update Limitation (DCU)
- ***Challenge***: Dell Command | Update returned a “System not supported by available catalogs” error despite using supported hardware
- ***Learning***: Behavior is commonly associated with catalog detection or repository compatibility issues
- ***Action***: Bypassed the automated utility in favor of a Manual Repository approach, downloading specific BIOS and chipset firmware directly from Dell Support to establish a known-good firmware state

## Hardware Specification
| Component          | Specification                                |
|------------------|----------------------------------------------|
| Model             | Dell PowerEdge R440                          |
| OS                | Windows Server 2019 Standard                 |
| CPU               | Intel Xeon Silver 4110 (8 cores)            |
| Memory            | 32 GB DDR4 (2 x 16 GB @ 2666 MHz)           |
| Storage Controller| PERC H330                                    |
| Storage           | 2 x 1TB SATA HDD                             |
| RAID              | RAID 1 (Virtual Disk 0)                      |
| Networking        | Broadcom 5720 Quad-Port 1 GbE (LOM)         |
| Power             | 1 x 550W PSU (non-redundant)                |
| Management        | iDRAC 9 (Basic Management)                  |

## Network Architecture & IP Schema
| Component       | Connection Type | IP Address     | Subnet Mask      | Role            |
|-----------------|----------------|---------------|-----------------|--------------------|
| Physical Router | -              | 192.168.4.1   | 255.255.252.0   | Network Exit Point | 
| iDRAC 9         | LOM1 (Shared)  | 192.168.6.219 | 255.255.252.0   | Hardware Management|
| R440 OS         | LOM1 (Static)  | 192.168.6.220 | 255.255.252.0   | Windows Server 2019|
| DNS 1           | External       | 8.8.8.8       | N/A             | Google Public DNS  |
| DNS 2           | External       | 1.1.1.1       | N/A             | Cloudflare Public DNS |

## System Recovery (Initial State)

### Initial System State
- Upon first boot, the pre-installed Windows Server environment was unstable and unreliable. Core components, including the Start Menu and system utilities, failed to respond consistently, indicating a compromised operating system. The system was unsuitable for production or lab use

### Risk Assessment & Probable Causes
- While the exact cause of the corruption could not be determined, the observed behavior was consistent with prior failed OS deployments, incomplete system preparation, or improper decommissioning. Regardless of origin, The system could not be trusted for repeatable cybersecurity or networking labs

### Build vs. Repair Decision
- Two recovery paths were evaluated: attempting an in-place repair using SFC and DISM, or performing a full bare-metal rebuild. While in-place repair may restore partial functionality, it cannot guarantee the elimination of latent configuration issues or residual artifacts. A clean installation was selected to establish a known-good baseline, this ensured consistent system behavior for future lab and forensic testing

---

## Deployment Process & Troubleshooting

### Phase 1: Preparing USB for OS Installation
- Windows Server 2019 was deployed via UEFI boot using a Rufus-prepared (GPT/UEFI, NTFS) USB after Virtual Media was ruled out due to iDRAC licensing constraints

### Phase 1.5: Storage Validation
- Before initiating OS deployment, the RAID 1 virtual disk was confirmed as healthy and fully synchronized before OS installation. The two physical 1TB SATA drives were confirmed to be fully abstracted into a single RAID 1 virtual disk, eliminating exposure of individual disks at the OS level
- Controller Health and virtual disk status were verified as optimal, confirming correct PERC H330 operation and parity synchronization. As a final pre-flight check, the Windows Server installer detected a single unallocated virtual disk, validating that the RAID configuration was correctly presented to the OS
- Validation ensures that any installation or stability issues encountered later could be isolated to the OS layer rather than underlying storage configuration

### Phase 2: Ethernet & iDRAC Configuration
- Due to limited physical networking infrastructure, a shared LOM interface was selected to provide both host network connectivity and iDRAC management access over a single Ethernet connection. This approach provided uninterrupted out-of-band management capabilities while operating within the constraint of a single available router port
- While sharing the management and host traffic is not ideal in production environments, it provided a practical balance between accessibility and resource availability for this lab environment. In a future iteration, management traffic would be isolated using a dedicated iDRAC NIC or a managed switch with VLAN segmentation to reduce exposure and improve security

### Phase 3: OS installation via Lifecycle Controller
- Windows Server 2019 was deployed using Dell Lifecycle Controller to ensure hardware-aware installation and proper integration with the PERC H330 storage controller. Prior to deployment, RAID configuration was validated and UEFI boot mode with Secure Boot enabled to align with modern firmware standards
- A manual installation path was selected to maintain full control over disk layout and system configuration, as no unattended deployment templates were in place at the time. Installation media was provided via a locally mounted USB Device, after remote virtual media was ruled out due to iDRAC licensing constraints

### Phase 4: iDRAC Security Hradening & Access Control
- The iDRAC management interface was hardened to reduce exposure of the hardware management plane. Default factory credentials were rotated to a unique, complex password to eliminate known credential risk
- Unnecessary legacy access methods were disabled in favor of encrypted management protocols, limiting administrative access to secure channels only. A static management IP address was assigned to ensure consistent access and prevent loss of control during host-level network changes or outages

### Phase 5: Driver Installation
- A manual firmware and driver update strategy was used to establish a stable and verifiable baseline for the system. Automated update tooling was evaluated but not used due to catalog compatibility limitations with the platform
- Core components were updated in a controlled sequence, prioritizing chipset drivers, management controller firmware, system BIOS, and storage controller firmware. Updating firmware in this order prevented driver mismatches and ensured stable hardware detection

### Phase 6: Remote Desktop Configuration
- The server was assigned a static IP address to ensure consistent remote access and avoid dependency on DHCP lease behavior. Required firewall rules were enabled to permit administrative connectivity while maintaining default host-level protections
- Remote Desktop access was configured with Network Level Authentication (NLA) enforced, reducing exposure to unauthenticated connection attempts and limiting resource usage prior to user authentication. Successful remote connectivity was validated from an external host, confirming secure administrative access to the system
- Remote access was limited to administrative use only and not exposed beyond the local network



