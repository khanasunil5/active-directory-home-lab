# Troubleshooting Scenarios – Active Directory Home Lab

This document outlines common Active Directory and Group Policy issues intentionally created and resolved to simulate real-world IT Support and Service Desk tasks.

---

## Issue 1: User Account Disabled / Password Access Revoked

### Scenario
A domain user is unable to log in due to their account being disabled in Active Directory.

### Objective
Demonstrate understanding of:
- User account states in Active Directory
- Account lockout vs account disablement
- Basic authentication troubleshooting

### Steps to Reproduce
1. Logged into DC01
2. Opened **Active Directory Users and Computers (ADUC)**
3. Navigated to the LAB_Users OU
4. Opened properties of user:
   - Name: Aaron Blair
   - Username: ablair
5. Disabled the account via:
   - Properties → Account → ✔ Account is disabled
     ![Account Disabled](https://github.com/khanasunil5/active-directory-home-lab/blob/d8d79e66260a0c120a108033a7aa22c20bbfe58e/screenshots/troubleshoot-account-disabled.png)
### Observed Behaviour
- Attempted to log in on CLIENT01 as `lab\ablair`
- Received an error message indicating the account was unavailable for sign-in
 ![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/613fc69362fdd7c7840c3b06288a131b7a122712/screenshots/client-login-error.png)
### Investigation
- Checked account status in ADUC
- Confirmed the account was explicitly disabled

### Resolution
- Returned to DC01
- Re-enabled the account by unchecking **Account is disabled**
- Ensured password status was valid
![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/a34419acf511e242775a7c8050c69393ebf4c337/screenshots/account-reenabled-password-reset.png)
### Verification
- Successfully logged into CLIENT01 as `lab\ablair`
- Confirmed normal access restored

---

## Issue 2: Trust Relationship Between Workstation and Domain Failed

### Error Message
> “The trust relationship between this workstation and the primary domain failed.”

### What This Demonstrates
- Understanding of domain trust relationships
- Computer accounts in Active Directory
- DNS dependency
- Standard Service Desk recovery process

---

### Step A: Create the Issue (Intentional Failure)

1. Logged into DC01
2. Opened **Active Directory Users and Computers**
3. Navigated to:
   - `lab.local → Computers`
4. Right-clicked `CLIENT01`
5. Selected **Disable Account**

This action intentionally breaks the trust relationship between the workstation and the domain.

---

### Step B: Observe the Failure on CLIENT01

- Powered on CLIENT01
- Attempted to log in as a domain user:
  - `lab\jsmith`
- Received the trust relationship error message
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
- DNS server pointed to the Domain Controller
- IP address within correct subnet
- Domain name resolution working

#### 2. Computer Account Verification
On DC01:
- Confirmed `CLIENT01` computer account still existed in ADUC
- Noted that the account had been recently reset

---

### Step D: Resolution

#### Method Used: Rejoin the Domain (Most Common)

1. On CLIENT01:
   - Changed domain membership to **WORKGROUP**
2. Restarted the system
3. Rejoined domain:
   - `lab.local`
4. Restarted again
![Screenshot](https://github.com/khanasunil5/active-directory-home-lab/blob/a34419acf511e242775a7c8050c69393ebf4c337/screenshots/trust-relationship-domain-rejoined.png)
---

### Step E: Verification

- Successfully logged in as `lab\jsmith`
- Confirmed Group Policies applied
- Ran:
- gpresult /r

- 
Trust relationship fully restored.

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




