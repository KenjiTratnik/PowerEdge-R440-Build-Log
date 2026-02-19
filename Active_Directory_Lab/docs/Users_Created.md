# Domain Users Created

## Overview
This document outlines the creation and verification of domain user accounts within the LAB.LOCAL Active Directory environment.

Domain Controller: LAB-DC1
Domain: LAB.LOCAL
Environment: Single-DC Lab Setup

---

## Users Created

### 1. WindowsUser1
- Location: OU=Users (Default Users Container)
- Account Type: Standard Domain User
- Password: Configured with domain password policy
- Status: Enabled
- Group Membership:
  - Domain Users

### 2. WindowsUser2
- Location: OU=Users (Default Users Container)
- Account Type: Standard Domain User
- Password: Configured with domain password policy
- Status: Enabled
- Group Membership:
  - Domain Users
 
---

## Creation Method

Users were created using:

Active Directory Users and Computers (ADUC)

Steps
1. Open ADUC
2. Navigate to Users container
3. Right-click -> New -> User
4. Enter user information
5. Set password and enable account

---

## Verification

### 1. Confirmed User Exists in AD
- Verified both users appear in ADUC

### 2. Domain Login Test (Windows 10 VM)
Logged into domain-joined Windows 10 VM using:

LAB\WindowsUser1
LAB\WindowsUser2

whoami

Output:
LAB\WindowsUser1
LAB\WindowsUser2

### 3. Domain Membership Verification

Command:
systeminfo | findstr /B /C:"Domain"

Output:
Domain: LAB.LOCAL

---

## Notes

- Users are standard domain accounts with no administrative privileges.
- Accounts successfully authenticated against the Domain Controller.
- No replication or authentication errors observed

