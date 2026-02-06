# Dell R440 Homelab Deployment

This document details the **bare-metal deployment of the Dell PowerEdge R440 server**, including OS installation, storage configuration, network setup, management hardening, and driver installation. The goal is to provide a **reproducible process for future lab or enterprise-grade deployments**.

---

## Phase 0: Initial Assessment

**Objective:** Evaluate the pre-installed environment and decide between repair or full rebuild.

- **Initial State:** Pre-installed Windows Server 2019 was unstable; core components (Start Menu, system utilities) were unresponsive.  
- **Decision:** Full bare-metal rebuild chosen to ensure a known-good baseline.  
- **Risk Assessment:** In-place repair could leave residual issues; clean install eliminates latent problems.

---

## Phase 1: Preparing USB for OS Installation

**Objective:** Create bootable media for Windows Server 2019.

1. Download the official Windows Server 2019 ISO from Microsoft.  
2. Use **Rufus** to prepare a USB stick:  
   - GPT partition scheme  
   - UEFI boot  
   - File system: NTFS  
3. Note: iDRAC Virtual Media was unavailable due to Basic license restrictions.

---

## Phase 1.5: Storage Validation

**Objective:** Ensure the RAID virtual disk is healthy and correctly presented.

- Controller: **PERC H330**  
- RAID: **RAID 1 (2 × 1TB SATA drives)**  
- Validation Steps:  
  1. Confirm virtual disk synchronization.  
  2. Verify no physical drives are exposed to the OS.  
  3. Confirm Windows installer detects a single unallocated virtual disk.

---

## Phase 2: Ethernet & iDRAC Configuration

**Objective:** Configure network and management access with limited infrastructure.

- Shared **LOM1 interface** for iDRAC and OS due to single available router port.  
- Assign **static IPs** for both iDRAC and host OS for consistent remote access.  
- Future improvement: Isolate management traffic with dedicated NIC or VLAN in production.

---

## Phase 3: OS Installation via Lifecycle Controller

**Objective:** Deploy Windows Server 2019 with proper hardware integration.

1. Launch **Lifecycle Controller** during POST.  
2. Validate RAID configuration and UEFI boot mode with Secure Boot.  
3. Mount USB locally and perform manual installation.  
4. Partition disk according to lab requirements.  
5. Confirm OS installation completes successfully.

---

## Phase 4: iDRAC Security Hardening

**Objective:** Reduce attack surface of management interface.

- Rotate default credentials to unique, complex password.  
- Disable unnecessary legacy access methods.  
- Enable encrypted protocols (HTTPS, SSH) for management access.  
- Assign static management IP to prevent loss of control during network changes.

---

## Phase 5: Driver and Firmware Installation

**Objective:** Establish a stable, verifiable hardware baseline.

1. Evaluate automated Dell Command Update (DCU) — **failed due to catalog detection**.  
2. Use manual repository approach:  
   - Update BIOS, chipset firmware, iDRAC firmware, storage controller drivers sequentially.  
   - Ensures correct detection of all hardware components.  
3. Verify installation success and hardware health via iDRAC.

---

## Phase 6: Remote Desktop Configuration

**Objective:** Enable secure remote administration of Windows Server.

- Assign **static IP** to avoid DHCP dependency.  
- Enable **Remote Desktop** with Network Level Authentication (NLA).  
- Configure firewall rules to allow administrative access only.  
- Validate remote connectivity from external host.

