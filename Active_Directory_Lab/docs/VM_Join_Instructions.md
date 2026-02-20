# Domain Client Join Instructions
This document outlines the process of joining a Windows 10 virtual machine to the LAB.LOCAL Active Directory domain hosted on the Dell PowerEdge R440 Domain Controller.

This procedure validates
- DNS Functionality
- Active Directory authentication
- Domain connectivity
- SYSVOL/NETLOGON availability
- Credential validation

## Environment Overview
***Domain Controller***
- Hostname: LAB_DC1
- Domain: LAB.LOCAL
- Roles: AD DS, DNS, Global Catalog
- Platform: Windows Server 2019

***Client Machine***
- OS: Windows 10 (Clean Installation)
- Network: Same subnet as Domain Controller
- Initial Account: Local Administrator

## 1. Pre-Join Requirements
Before attempting to join the domain, confirm:

### 1.1 Network Connectivity
From the Windows 10 VM:

  `ping LAB-DC1`

### 1.2 DNS Configuration
The client must use the Domain Controller as its DNS server.

Check current DNS:

  `ipconfig /all`

If not pointing to the DC IP, configure manually:

  `Set-DnsClientServerAddress -InterfaceAlias “Ethernet” -ServerAddress <DC_IP>`

Verify:

  `nslookup LAB.LOCAL`

## 2. Domain Join Procedure

### 2.1 System Settings
- Right click ***Start***
- Select ***System***
- Click ***Rename this PC (Advanced)***
- Select ***Change***

### 2.2 Join Domain
- Select ***Domain***
- Enter:

    `LAB.LOCAL`

### 2.3 Provide Domain Credentials
When promoted, enter credentials for an existing domain user:

Example:

  `
  Username: LAB\WindowsUser1
  Password: <User Password>
  `

or

  `
  Username: WindowsUser1@LAB.LOCAL
  `

Credentials must belong to:
- Domain Administrator
Or
- A domain user with permission to join machines

### 2.4 Restart
Upon successful authentication, should receive:

  `”Welcome to the LAB.LOCAL domain.”`

Restart the VM when promoted.

## 3. Post-Join Validation

### 3.1 Confirm Domain Membership
Log in using domain credentials:

  `LAB\WindowsUser1`

Then verify:

  `systeminfo | findstr /B /C:”Domain”`

Expected Output:

  `Domain: LAB.LOCAL`

### 3.2 Verify Domain Trust
Run:

  `whoami`

Expected:

  `lab\windowsuser1`

### 3.3 Confirm Secure Channel

  `nltest /sc_verify:LAB.LOCAL`

Expected:

  `Status = 0 0x0 NERR_Success`

