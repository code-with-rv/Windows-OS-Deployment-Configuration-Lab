# Windows-OS-Deployment-Configuration-Lab
# Windows OS Deployment & Configuration Lab

![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?logo=powershell&logoColor=white)
![Security](https://img.shields.io/badge/Focus-Baseline%20Hardening-2E7D32)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen)

> Installed and configured Windows, created role-based user accounts, and applied baseline security hardening (password policy, disabled services, firewall rules) across 2 machines.

---

## Table of Contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Lab Architecture](#lab-architecture)
- [Environment](#environment)
- [Implementation Phases](#implementation-phases)
- [Role-Based Access Control](#role-based-access-control)
- [Security Baseline](#security-baseline)
- [Verification & Validation](#verification--validation)
- [Screenshots](#screenshots)
- [Troubleshooting](#troubleshooting)
- [Skills Demonstrated](#skills-demonstrated)
- [Repository Structure](#repository-structure)
- [Future Improvements](#future-improvements)
- [License](#license)

---

## Overview

This lab simulates a real-world Windows workstation deployment. It covers the full lifecycle of preparing a Windows endpoint for use in a managed environment:

1. Installing and configuring Windows on each machine
2. Creating role-based local user accounts with least-privilege permissions
3. Hardening each system against common attack vectors
4. Verifying that every control is applied and effective

The goal is to demonstrate the workflow a system administrator or security-minded IT technician follows when provisioning endpoints that must meet a defined security baseline.

## Objectives

- Deploy and configure Windows on **2 machines** with consistent settings
- Design and implement **role-based user accounts** using the principle of least privilege
- Enforce a **password policy** aligned with common compliance guidance
- **Disable unnecessary services** to reduce attack surface
- Configure **Windows Defender Firewall** rules to allow only required traffic
- Document and **verify** each configuration step with evidence

## Lab Architecture

```
┌──────────────────────────┐         ┌──────────────────────────┐
│        MACHINE 01        │         │        MACHINE 02        │
│  OS: Windows [version]   │         │  OS: Windows [version]   │
│  Role: [e.g., Standard]  │         │  Role: [e.g., Admin]     │
│  Users: [user1, user2]   │         │  Users: [user3, user4]   │
│  Hardening: Applied      │         │  Hardening: Applied      │
└──────────────────────────┘         └──────────────────────────┘
```

> Replace with a diagram of your actual setup, or describe whether the machines are VMs (Hyper-V / VirtualBox / VMware) or physical hosts.

## Environment

| Component        | Details                          |
|------------------|----------------------------------|
| Operating System | Windows [10 / 11 / Server 20XX]  |
| Edition          | [Pro / Enterprise / Home]        |
| Virtualization   | [Hyper-V / VirtualBox / Physical]|
| Machines         | 2                                |
| Admin Tools      | PowerShell, Local Users and Groups, Local Security Policy, Windows Firewall with Advanced Security |

## Implementation Phases

### Phase 1: Windows Installation & Initial Configuration

- Installed Windows on each machine
- Set computer names, time zone, and region settings
- Applied all available updates via Windows Update
- Verified system information:

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber
```

### Phase 2: Role-Based User Accounts

Users were created according to the responsibilities of each role, with no unnecessary administrator rights granted.

```powershell
# Create a standard user
net user [username] [password] /add /fullname:"[Full Name]" /passwordchg:yes

# Add to the appropriate group
net localgroup Users [username] /add

# Add to Administrators only if the role requires it
net localgroup Administrators [username] /add
```

### Phase 3: Password Policy Hardening

Configured via Local Security Policy (`secpol.msc`) or the command line:

```powershell
net accounts /minpwlen:12 /maxpwage:90 /minpwage:1 /uniquepw:5 /lockoutthreshold:5 /lockoutduration:30 /lockoutwindow:30
```

| Setting                       | Value       |
|-------------------------------|-------------|
| Minimum password length       | 12 characters |
| Maximum password age          | 90 days     |
| Minimum password age          | 1 day       |
| Password history enforced     | 5 passwords |
| Account lockout threshold     | 5 attempts  |
| Lockout duration              | 30 minutes  |

### Phase 4: Service Hardening

Unnecessary services were disabled to reduce the attack surface. Each service was reviewed before changes were made.

```powershell
# Example: disable a service that is not required for this role
Stop-Service -Name [ServiceName] -Force
Set-Service -Name [ServiceName] -StartupType Disabled
```

| Service Name        | Display Name            | Action    | Reason                            |
|---------------------|-------------------------|-----------|-----------------------------------|
| [Service1]          | [Display Name]          | Disabled  | Not needed for this role          |
| [Service2]          | [Display Name]          | Disabled  | Legacy protocol, unused           |
| [Service3]          | [Display Name]          | Disabled  | Reduces remote attack surface     |

### Phase 5: Firewall Configuration

Windows Defender Firewall was enabled on all profiles, and inbound rules were restricted to required traffic only.

```powershell
# Enable firewall on all profiles
Set-NetFirewallProfile -Profile Domain,Private,Public -Enabled True

# Block a specific inbound port (example)
New-NetFirewallRule -DisplayName "Block Telnet" -Direction Inbound -Protocol TCP -LocalPort 23 -Action Block

# Allow only required application traffic (example)
New-NetFirewallRule -DisplayName "Allow [App]" -Direction Inbound -Program "[Path\To\App.exe]" -Action Allow
```

| Rule Name        | Direction | Protocol | Port  | Action | Purpose             |
|------------------|-----------|----------|-------|--------|---------------------|
| [Rule 1]         | Inbound   | TCP      | [port]| Allow  | [purpose]           |
| Block Telnet     | Inbound   | TCP      | 23    | Block  | Insecure protocol   |

## Role-Based Access Control

| Machine   | Username   | Role            | Group(s)        | Privileges                  |
|-----------|------------|-----------------|-----------------|-----------------------------|
| Machine 01| [user1]    | [Standard User] | Users           | Basic application access    |
| Machine 01| [user2]    | [Administrator] | Administrators  | Full local administration   |
| Machine 02| [user3]    | [Standard User] | Users           | Basic application access    |
| Machine 02| [user4]    | [Restricted]    | Users           | Limited, role-specific apps |

**Principle applied:** Each account receives only the permissions its role requires. Administrative rights are reserved for accounts that explicitly need them.

## Security Baseline

The baseline below summarizes the controls applied to both machines:

| Control Area       | Configuration                          | Status   |
|--------------------|----------------------------------------|----------|
| OS Updates         | All updates installed                  | ✅ Done  |
| Password Policy    | Min length 12, complexity enabled      | ✅ Done  |
| Account Lockout    | 5 attempts / 30-minute lockout         | ✅ Done  |
| Unused Services    | Disabled and documented                | ✅ Done  |
| Firewall           | Enabled on all profiles                | ✅ Done  |
| Inbound Rules      | Restricted to required traffic         | ✅ Done  |
| User Privileges    | Least privilege applied                | ✅ Done  |

## Verification & Validation

Each control was tested after implementation:

```powershell
# Confirm password policy
net accounts

# Confirm firewall status
Get-NetFirewallProfile | Select-Object Name, Enabled

# Confirm service state
Get-Service -Name [ServiceName] | Select-Object Name, Status, StartType

# List local group membership
Get-LocalGroupMember -Group Administrators

# List local users and their status
Get-LocalUser | Select-Object Name, Enabled, LastLogon
```

**Results:** [Summarize what passed, e.g., "All password policy values match the target configuration on both machines. The firewall is active on all three profiles, and only the defined inbound rules are present."]

## Screenshots

Evidence for each phase is stored in the [`Screenshots/`](./Screenshots) folder.

| Phase | Description                    | Preview |
|-------|--------------------------------|---------|
| 1     | Windows installation summary   | [Screenshots/phase1.png] |
| 2     | User account creation          | [Screenshots/phase2.png] |
| 3     | Password policy configuration  | [Screenshots/phase3.png] |
| 4     | Disabled services              | [Screenshots/phase4.png] |
| 5     | Firewall rules                 | [Screenshots/phase5.png] |

## Troubleshooting

| Issue                                      | Cause                                   | Resolution                                                  |
|--------------------------------------------|-----------------------------------------|-------------------------------------------------------------|
| `net accounts` changes not reflected       | Policy overridden by Group Policy       | Run `gpupdate /force` and recheck                           |
| Service fails to disable                   | Dependent service still running         | Check dependencies with `Get-Service -Name X -DependentServices` |
| Application blocked after firewall change  | Missing allow rule for the program      | Add an inbound rule scoped to the executable path           |
| "Access denied" when creating users        | Command not run as administrator        | Launch PowerShell with "Run as administrator"               |

## Skills Demonstrated

- Windows installation and system configuration
- Local user and group management
- Password policy and account lockout configuration
- Service management and attack surface reduction
- Windows Defender Firewall configuration
- PowerShell scripting and command-line administration
- Security baseline design and validation
- Technical documentation

## Repository Structure

```
Windows-OS-Deployment-Configuration-Lab/
├── Screenshots/        # Evidence for each implementation phase
├── README.md           # Project documentation
└── [scripts/]          # Optional: PowerShell scripts used in the lab
```

## Future Improvements

- [ ] Automate the full baseline with a PowerShell script
- [ ] Add audit policy configuration (`auditpol`)
- [ ] Enforce settings through Group Policy or Intune
- [ ] Add BitLocker drive encryption
- [ ] Map controls to CIS Benchmarks for Windows
- [ ] Integrate Microsoft Defender for Endpoint
- [ ] Expand to a domain-joined Active Directory environment

## License

This project is licensed under the [MIT License](LICENSE). *(Change this to match your preference, or remove this section.)*

---

**Author:** Ravi Kumar
· [GitHub: @code-with-rv](https://github.com/code-with-rv)
