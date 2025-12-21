# Active Directory Home Lab (IT Support / Service Desk)

## Overview
This project documents a hands-on Active Directory home lab built to practise real-world IT Support and Service Desk tasks. The lab uses Windows Server 2019 and Windows 11 in a virtualised environment and covers user account management, Group Policy configuration, permissions, and troubleshooting common domain-related issues.

The goal of this lab was to get hands-on experience with everyday IT Support tasks such as managing user accounts, applying security policies, fixing login issues, and troubleshooting Group Policy problems.

---

## Technologies Used
- VMware Workstation
- Windows Server 2019 (Domain Controller)
- Windows 11 (Client)
- Active Directory Domain Services (AD DS)
- DNS
- Group Policy Management
- PowerShell (basic troubleshooting)

---

## Lab Architecture
- **Domain Controller:** DC01  
- **Client Machine:** CLIENT01  
- **Domain:** lab.local  
- **Network Type:** Host-only  
- **IP Scheme:**  
  - DC01: 192.168.100.10 (Static IP)

---

## Virtualisation & Performance Optimisation
As the host machine has 8 GB of RAM, the lab was configured with performance and stability in mind. VMware memory usage was limited to avoid host system issues, and the Domain Controller and client VM were only run together when necessary. Windows visual effects were disabled inside the virtual machines, and unused VMs were paused or shut down after testing. This approach mirrors real-world scenarios where systems must be managed within resource constraints.

---

## Domain Controller Configuration
- Installed and configured Windows Server 2019 (Desktop Experience)
- Renamed the server to DC01
- Configured a static IP address for domain stability
- Installed Active Directory Domain Services and DNS
- Promoted the server to a Domain Controller and created the lab.local domain

---

## Client Configuration
- Installed Windows 11 on a client virtual machine 
- successfully joined it to the lab.local domain
- Verified domain authentication by logging in with Active Directory user accounts

---

## Active Directory Structure
- Designed and organised the Active Directory structure by creating Organizational Units (OUs) for Users, Workstations, IT, and HR
- Created and managed user accounts and security groups within Active Directory
- Performed common user lifecycle administration tasks including:
  - Creating new user accounts
  - Resetting passwords
  - Enabling and disabling user accounts
  - Managing security group membership


---

## Group Policy Configuration

### GPO 1 — Domain Password Policy
Reviewed and configured the Default Domain Policy to align with standard security best practices. Most password settings were already appropriately configured, so the primary change made was increasing the minimum password length to strengthen account security.

The final password policy settings were:
Minimum password length: increased from 7 to 10 characters
Password complexity: enabled
Minimum password age: 1 day
Maximum password age: 42 days
Password history: 24 passwords remembered

This configuration reflects a realistic domain environment where existing policies are assessed and adjusted rather than rebuilt from scratch.
---

### GPO 2 — Disable USB Storage
**Scope:** Workstations OU  

I created a Group Policy to block access to removable storage devices on domain-joined workstations using Administrative Templates. While verifying the policy on CLIENT01, the USB restriction did not appear to be applied when running `gpresult /r`. After troubleshooting, I identified that this was a computer-based policy and re-ran the command using `gpresult /r /scope computer`, which confirmed the GPO was successfully applied. This reinforced the difference between user and computer Group Policy scopes and the importance of using the correct troubleshooting commands.


**Skill demonstrated:** Endpoint security controls and prevention of unauthorised data exfiltration.

---

### GPO 3 — Standard Desktop Wallpaper
**Scope:** Users OU  
- Created and shared a central wallpaper repository on DC01
- Deployed a standard desktop wallpaper using User Configuration policies

**Skill demonstrated:** User environment standardisation and GPO validation.

---

### GPO 4 — Disable Control Panel Access
**Scope:** Users OU  
- Blocked access to Control Panel to reduce user misconfiguration risks

**Skill demonstrated:** Reducing user error through policy-based restrictions.

---

## Troubleshooting Scenarios

### Issue 1 — Disabled User Account / Password Revocation
**Scenario:**  
- Disabled a user account (ablair) in Active Directory
- Attempted login resulted in authentication failure
- Re-enabled account and restored access

**Skills demonstrated:**  
- Account status troubleshooting  
- User authentication workflows  

---

### Issue 2 — Trust Relationship Failure
**Error:**  
> “The trust relationship between this workstation and the primary domain failed.”

**Cause (introduced intentionally):**
- Reset CLIENT01 computer account in Active Directory

**Troubleshooting steps:**
- Verified DNS configuration on client
- Confirmed IP address and domain connectivity
- Checked computer account status in ADUC

**Resolution:**
- Rejoined CLIENT01 to the domain
- Verified successful authentication and Group Policy application

**Skills demonstrated:**
- Domain trust concepts
- Computer accounts in Active Directory
- DNS dependency in authentication
- Structured troubleshooting methodology

---

### Issue 3 — Drive Mapping GPO Not Applying
**Scenario:**
- Created shared HR network folder with correct NTFS and share permissions
- Created HR Users security group and assigned user
- Configured drive mapping GPO using Group Policy Preferences
- Intentionally broke policy by:
  - Moving user to incorrect OU
  - Applying GPO to wrong scope

**Troubleshooting steps:**
- Forced policy update (gpupdate /force)
- Checked applied user policies using:
  - gpresult /r /scope user
- Identified missing or denied GPO

**Resolution:**
- Corrected OU placement
- Ensured GPO used User Configuration
- Verified mapped drive appeared successfully

**Skills demonstrated:**
- User vs Computer GPO scope
- Group Policy Preferences
- File share permissions
- Real-world Service Desk troubleshooting

---

## What This Project Demonstrates
- Hands-on experience with Active Directory administration
- Realistic IT Support troubleshooting scenarios
- Understanding of Group Policy design and scope
- Ability to document technical work clearly
- Security-conscious configuration decisions

---

## Screenshots & Documentation
- Screenshots of configurations, errors, and resolutions are included in the `/screenshots` directory
- Detailed troubleshooting notes available in `/documentation`

---

## Key Takeaway
This lab mirrors the types of issues handled daily by IT Support and Service Desk teams and demonstrates readiness for an entry-level IT role through practical, documented experience.
