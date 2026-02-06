# Homelab Architecture

This document provides a **detailed overview of the hardware, storage, and network architecture** of the Dell PowerEdge R440 homelab. It is intended to give readers a clear understanding of the system’s design, components, and connections.

---

## Hardware Overview

| Component               | Specification                                         | Notes                                    |
|-------------------------|-------------------------------------------------------|-----------------------------------------|
| Server Model            | Dell PowerEdge R440                                   | 1U rackmount, enterprise-grade          |
| CPU                     | Intel Xeon Silver 4110 (8 cores, 2.1 GHz)           | Supports virtualization and lab workloads |
| Memory                  | 32 GB DDR4 (2 × 16 GB @ 2666 MHz)                   | Configured for moderate lab tasks       |
| Storage Controller      | PERC H330 RAID Controller                             | RAID 1 configured for redundancy        |
| Storage Drives          | 2 × 1 TB SATA HDD                                     | RAID 1 for OS and lab data             |
| RAID Configuration      | RAID 1 (Virtual Disk 0)                               | Mirrors two physical disks             |
| Network Adapter         | Broadcom 5720 Quad-Port 1 GbE (LOM)                  | Shared iDRAC and OS traffic via LOM1   |
| Power Supply            | 1 × 550W PSU (non-redundant)                         | Single power source for lab environment |
| Management Controller   | iDRAC 9 Basic                                         | Remote server management                |

---

## Network Architecture

The server is integrated into an existing /22 subnet to avoid disrupting other devices. Due to limited physical networking infrastructure, **iDRAC and host traffic share a single NIC (LOM1)**.  

**Key Points:**
- Static IP addresses for iDRAC and host OS ensure consistent access.  
- Management traffic would ideally be isolated in production environments using a dedicated NIC or VLAN.  
- Public DNS resolvers are used during initial deployment due to the absence of an internal DNS server.

**Network Table:**

| Component        | Connection Type | IP Address      | Subnet Mask      | Role                         | Gateway        |
|-----------------|----------------|----------------|-----------------|-------------------------------|----------------|
| Physical Router  | N/A            | 192.168.4.1    | 255.255.252.0   | Network Exit Point            | N/A            |
| iDRAC 9         | LOM1 (Shared)  | 192.168.6.219  | 255.255.252.0   | Hardware Management           | 192.168.4.1    |
| R440 OS          | LOM1 (Static)  | 192.168.6.220  | 255.255.252.0   | Windows Server 2019           | 192.168.4.1    |
| DNS 1            | External       | 8.8.8.8        | N/A             | Public DNS Resolver           | N/A            |
| DNS 2            | External       | 1.1.1.1        | N/A             | Public DNS Resolver           | N/A            |

---

## Storage Architecture

- **RAID Controller:** PERC H330  
- **Physical Drives:** 2 × 1 TB SATA HDD  
- **RAID Level:** RAID 1 (mirrored)  
- **Purpose:** Ensures redundancy and isolates OS installation from individual disk failures.  
- **Validation:** Virtual disk is presented to the OS as a single logical drive.  
- **Notes:** RAID health is verified pre-OS deployment to avoid storage-related errors.

---

## Management and Remote Access

- **iDRAC 9 Basic Management**
  - Provides out-of-band management for firmware updates, system health monitoring, and remote console access (limited by Basic license).  
  - IP: 192.168.6.219 (static)  
  - Shared with host OS over LOM1 due to limited router ports.  

- **Remote Desktop**
  - Configured on Windows Server 2019 with NLA enforced.  
  - IP: 192.168.6.220 (static)  

**Future Improvements:**  
- Dedicated iDRAC NIC or VLAN for isolated management traffic  
- Redundant power supplies for high availability  

---

## Diagram (Conceptual)

```text
         +-----------------+
         | Physical Router |
         | 192.168.4.1     |
         +--------+--------+
                  |
                  | /22 subnet
                  |
         +--------+----------------------+
         | Dell PowerEdge R440           |
         |-------------------------------|
         | iDRAC 9: 192.168.6.219       |
         | OS: 192.168.6.220            |
         | LOM1 (Shared NIC)            |
         +-------------------------------+
                  |
             External DNS
         +------------+------------+
         | 8.8.8.8   | 1.1.1.1    |

