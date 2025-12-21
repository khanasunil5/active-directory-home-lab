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

---

### GPO 2 — Disable USB Storage
**Scope:** Workstations OU  

I created a Group Policy to block access to removable storage devices on domain-joined workstations using Administrative Templates. While verifying the policy on CLIENT01, the USB restriction did not appear to be applied when running `gpresult /r`. After troubleshooting, I identified that this was a computer-based policy and re-ran the command using `gpresult /r /scope computer`, which confirmed the GPO was successfully applied. This reinforced the difference between user and computer Group Policy scopes and the importance of using the correct troubleshooting commands.

---

### GPO 3 — Standard Desktop Wallpaper
- Deployed a standard desktop wallpaper using Group Policy Preferences (User Configuration)
- Configured registry-based policy settings to:
  - Set the wallpaper path from a centrally shared location
  - Enforce the wallpaper style (Fill)
  - Prevent users from changing the desktop background
- Verified the policy applied successfully to domain users on the client machine



---

### GPO 4 — Disable Control Panel Access
**Scope:** Users OU  
- Blocked access to Control Panel to reduce user misconfiguration risks

---

## Troubleshooting Scenarios

### Issue 1 — Disabled User Account / Password Revocation
Disabled a domain user account (ablair) in Active Directory to simulate a real user access issue. Verified that login attempts failed as expected, then re-enabled the account and confirmed successful authentication.

Skills demonstrated:
- Active Directory user account management
- Diagnosing account-related login failures
- Understanding of domain authentication behaviour

---

### Issue 2 — Trust Relationship Failure
Error encountered: “The trust relationship between this workstation and the primary domain failed.”
To replicate a common Service Desk issue, I intentionally reset the CLIENT01 computer account in Active Directory Users and Computers, which caused the trust relationship between the workstation and the domain to fail.

I then logged into CLIENT01 using the local administrator account and began troubleshooting. I checked the network configuration to confirm the client was receiving a valid IP address and that DNS was correctly pointing to the Domain Controller. I verified network connectivity by confirming communication with the Domain Controller.

Finally, I reviewed the CLIENT01 computer account in Active Directory and confirmed that the account had been reset, identifying this as the cause of the trust relationship failure.

To fix the issue, I removed CLIENT01 from the domain and joined it to a workgroup. I then rejoined the machine to the lab.local domain using domain admin credentials. After restarting the system, I confirmed successful domain login and checked that Group Policies were applying correctly using `gpresult /r`.

What this demonstrates:
Understanding of how domain trust relationships work
Knowledge of computer accounts in Active Directory
Awareness of DNS as a critical dependency for authentication

---

### Issue 3 — Drive Mapping GPO Not Applying
I set up a shared HR network folder on the domain controller and configured both share permissions and NTFS permissions correctly. I then created an HR Users security group in Active Directory, added a test user to the group, and configured a drive mapping Group Policy using Group Policy Preferences to map the share to a drive letter for HR users.

To test my understanding of Group Policy behaviour, I intentionally caused the drive mapping to fail by moving the user into an incorrect OU and by applying the GPO to the wrong scope.

When the mapped drive did not appear on the client machine, I began troubleshooting by forcing a policy update using gpupdate /force and reviewing the applied user policies with gpresult /r /scope user. This allowed me to confirm that the GPO was not being applied and identify whether it was missing or denied.

I resolved the issue by moving the user back into the correct OU and ensuring the drive mapping was configured under User Configuration rather than Computer Configuration. After updating policies and logging back in, the network drive mapped successfully.

This exercise demonstrates:

Understanding of User vs Computer Group Policy scope

Use of Group Policy Preferences for drive mapping

Knowledge of NTFS and share permissions

A structured, real-world Service Desk troubleshooting approach
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
