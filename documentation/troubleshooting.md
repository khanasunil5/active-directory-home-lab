# Troubleshooting Scenarios – Active Directory Home Lab

This document outlines common Active Directory and Group Policy issues intentionally created and resolved to simulate real-world IT Support and Service Desk tasks.

---

## Issue 1: User Account Disabled / Password Access Revoked

### Scenario
A domain user is unable to log in due to their account being disabled in Active Directory.

### Objective
Learn how user accounts behave in Active Directory by disabling an account, observing the login error, and restoring access through basic troubleshooting.

### Steps to Reproduce
I accessed the domain controller and opened Active Directory Users and Computers. From the LAB_Users OU, I opened the properties for the user Aaron Blair (ablair) and disabled the account using the account settings.
     ![Account Disabled](https://github.com/khanasunil5/active-directory-home-lab/blob/d8d79e66260a0c120a108033a7aa22c20bbfe58e/screenshots/troubleshoot-account-disabled.png)
### Observed Behaviour
After attempting to log in on CLIENT01, the user recieved an error message indicating the account was unavailable for sign-in.
 ![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/613fc69362fdd7c7840c3b06288a131b7a122712/screenshots/client-login-error.png)
### Investigation
Checked the account status in ADUC and confirmed that the account was explicitly disabled.
### Resolution
I returned to DC01 and re-enabled the user account by unchecking Account is disabled, ensuring the account was able to authenticate successfully.
![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/a34419acf511e242775a7c8050c69393ebf4c337/screenshots/account-reenabled-password-reset.png)
### Verification
Successfully logged into CLIENT01.

---

## Issue 2: Trust Relationship Between Workstation and Domain Failed

### Error Message
> “The trust relationship between this workstation and the primary domain failed.”

### What This Demonstrates
This issue demonstrates an understanding of how domain trust relationships work, how computer accounts are managed in Active Directory, the role DNS plays in authentication, and the standard recovery steps used by Service Desk teams.

---

### Step A: Create the Issue (Intentional Failure)

1. Logged into DC01
2. Opened **Active Directory Users and Computers**
3. Navigated to:
   - `lab.local → Computers`
4. Right-clicked `CLIENT01`
5. Selected **Disable Account**

This intentionally broke the trust relationship between the workstation and the domain.

---

### Step B: Observe the Failure on CLIENT01
attempted to log in on CLIENT01 using the domain account, which resulted in a trust relationship error message.
![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/a34419acf511e242775a7c8050c69393ebf4c337/screenshots/trust-relationship-error-client.png)
---

### Step C: Troubleshooting Process

#### 1. Network & DNS Verification
Logged into CLIENT01 using the local administrator account.

Commands executed:
- ipconfig /all
- ping 192.168.100.10
- ping lab.local

Verified:
- Verified that the DNS server was correctly pointing to the Domain Controller
- Confirmed the client IP address was within the correct subnet
- Successfully resolved the domain name, confirming name resolution was working

#### 2. Computer Account Verification
On DC01:
- Confirmed `CLIENT01` computer account still existed in ADUC
- Noted that the account had been recently reset

---

### Step D: Resolution

#### Method Used: Rejoin the Domain (Most Common)

On CLIENT01, I removed the machine from the domain by switching it to a workgroup, restarted the system, and then rejoined it to the lab.local domain followed by a final restart.
![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/a34419acf511e242775a7c8050c69393ebf4c337/screenshots/trust-relationship-domain-rejoined.png)
---

### Step E: Verification
Successfully logged in as a domain user and confirmed that Group Policies were applied by running gpresult /r. The trust relationship was fully restored.
---

## Issue 3: Drive Mapping GPO Not Applying

### Scenario
A mapped network drive (H:) failed to appear for an HR user.

### Why This Scenario Matters
Demonstrates understanding of:
- User vs Computer GPO scope
- Group Policy Preferences
- Network share permissions
- Real-world GPO troubleshooting

---

### Part A: Shared Folder Setup

On DC01:
- Created folder: `C:\Shared\HR`
- Shared as: `\\DC01\HR`

Permissions:
- Share:
  - HR Users → Read
  - IT Admins → Full Control
- NTFS:
  - HR Users → Read
  - IT Admins → Full Control

---

### Part B: AD Objects

- Created group: **HR Users** (Security, Global)
- Created user: `hruser1`
- Added `hruser1` to **HR Users**

---

### Part C: Drive Mapping GPO Creation

- Created GPO: **Map HR Drive**
- Linked to **HR OU**
- Configured:
  - User Configuration
  - Preferences → Drive Maps
  - Drive Letter: H:
  - Location: `\\DC01\HR`

---

### Part D: Break the Configuration

Issue introduced by:
- Moving `hruser1` to an OU without the GPO
  **or**
- Applying the GPO incorrectly under Computer Configuration

Result:
- Logged into CLIENT01 as `lab\hruser1`
- H: drive not present

---

### Part E: Troubleshooting

Commands executed on CLIENT01:
- gpupdate /force
- gpresult /r /scope user
![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/a34419acf511e242775a7c8050c69393ebf4c337/screenshots/drive-mapping-gpo-missing.png)
Findings:
- GPO missing from applied user policies
- Confirmed scope and OU placement issue

---

### Part F: Resolution

- Moved user back into correct HR OU
- Ensured GPO:
  - Was linked correctly
  - Used User Configuration
- Forced policy update
- Logged off and logged back in

---

### Verification

- H: drive successfully mapped
- `gpresult /r /scope user` showed **Map HR Drive** applied
![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/a34419acf511e242775a7c8050c69393ebf4c337/screenshots/drive-mapping-gpo-fixed.png)




