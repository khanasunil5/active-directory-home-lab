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
   - ![Account Disabled](https://github.com/khanasunil5/active-directory-home-lab/blob/d8d79e66260a0c120a108033a7aa22c20bbfe58e/screenshots/troubleshoot-account-disabled.png)
### Observed Behaviour
- Attempted to log in on CLIENT01 as `lab\ablair`
- Received an error message indicating the account was unavailable for sign-in

### Investigation
- Checked account status in ADUC
- Confirmed the account was explicitly disabled

### Resolution
- Returned to DC01
- Re-enabled the account by unchecking **Account is disabled**
- Ensured password status was valid

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
5. Selected **Reset Account**

This action intentionally breaks the trust relationship between the workstation and the domain.

---

### Step B: Observe the Failure on CLIENT01

- Powered on CLIENT01
- Attempted to log in as a domain user:
  - `lab\jsmith`
- Received the trust relationship error message

---

### Step C: Troubleshooting Process

#### 1. Network & DNS Verification
Logged into CLIENT01 using the local administrator account.

Commands executed:

