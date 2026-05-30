# Enterprise Active Directory Attack & Defense Laboratory

## 📌 Project Overview
This repository serves as a comprehensive, hands-on technical journal of building, configuring, and securing a corporate Active Directory (AD) infrastructure. Designed from a Red Team perspective, this project documents the entire lifecycle of enterprise network exploitation and hardening.

The main goal is to understand deep architectural flaws, misconfigurations, and post-exploitation techniques within a Windows Domain environment.

---

## 🛠️ Network Architecture & Lab Topology

The laboratory is fully virtualized inside **Oracle VirtualBox** using an isolated host-only network to prevent any external impact.

| Machine Role | Operating System | Purpose / Services |
| :--- | :--- | :--- |
| **Domain Controller (DC)** | Windows Server 2022 | Active Directory Domain Services (AD DS), DNS, DHCP |
| **Workstation** | Windows 11 Enterprise | Target user environment, simulated corporate employee |
| **Attacking Platform** | Kali Linux | Network scanning, credential harvesting, exploitation |

---

## 🚀 Roadmap & Milestones

- [ ] **Phase 1: Infrastructure Deployment**
  - [ ] Install and configure Windows Server 2022 (Domain Controller)
  - [ ] Set up a fictional forest domain (`cybercorp.local`)
  - [ ] Configure Windows 11 Enterprise and join it to the domain
  - [ ] Create mock enterprise users, groups, and Service Account Profiles

- [ ] **Phase 2: Red Team Exploitation & Assessments**
  - [ ] LLMNR/NBT-NS Poisoning and SMB Relaying (using *Responder*)
  - [ ] Active Directory Enumeration (*BloodHound*, *CME*)
  - [ ] Kerberoasting & AS-REP Roasting attacks
  - [ ] Lateral Movement & Privilege Escalation to Domain Admin

- [ ] **Phase 3: Blue Team Hardening (Defense)**
  - [ ] Implementing Least Privilege Principles
  - [ ] Securing Kerberos authentication and monitoring event logs
  - [ ] # 💀 Day 1: Active Directory Reconnaissance & Initial Access Setup

## 🎯 Objective
Perform initial unauthenticated reconnaissance against the target domain controller, verify domain security policies (SMB Signing), evaluate anonymous enumeration vectors, and establish initial low-privileged access for an Assumed Compromise scenario.

---

## 🛠️ Infrastructure Overview
* **Target Domain:** `cybercorp.local`
* **Domain Controller (DC) IP:** `10.0.2.10` (Hostname: `CYBERCORP-DC`)
* **Attacker Host:** `10.0.2.30` (Kali Linux)
* **Network Environment:** Isolated internal lab (`intnet`)

---

## 🔍 Technical Execution & Results

### 1. SMB Signing Policy Verification
We used `NetExec` to discover the target and verify its SMB configuration:
```bash
netexec smb 10.0.2.10
Result: Windows Server 2022 Build 20348 x64 (signing:True) (SMBv1:False)

Security Assessment: SMB Signing is explicitly required, which mitigates standard unauthenticated SMB Relay attacks against this host. SMBv1 is properly disabled.
2. Unauthenticated Enumeration Hardening Check
We attempted multiple methods to extract domain information anonymously (Null Sessions):
# Attempting SAM database extraction over SMB
netexec smb 10.0.2.10 -u '' -p '' --users

# Attempting MSRPC SAMR binding
rpcclient -U "" -N 10.0.2.10 -c "enumdomusers"

# Running low-level SAMR dump
impacket-samrdump 10.0.2.10
Result: STATUS_SUCCESS with an empty user list on NetExec, and explicit NT_STATUS_ACCESS_DENIED errors on both rpcclient and samrdump.

Defensive Posture Assessment: The Windows Server 2022 target exhibits a hardened default configuration. Unauthenticated user enumeration via SMB or MSRPC interfaces is effectively blocked.
3. Establishing Initial Access (Assumed Compromise)
Since anonymous extraction was unsuccessful, we simulated a successful initial foothold (e.g., via a phishing vector) by provisioning a low-privileged domain account (s.abdullaev) within the Users container via dsa.msc.

We verified the credentials from the attacker machine using NetExec:

Bash
netexec smb 10.0.2.10 -u 's.abdullaev' -p 'Welcome2026!'
Result: [+] cybercorp.local\s.abdullaev:Welcome2026!

Status: Authentication successful. The account is confirmed valid, active, and ready for authenticated domain enumeration.
🚀 Next Steps (Day 2 Plans)
With valid low-privileged domain credentials established, the next phase will focus on internal enumeration:

Perform comprehensive authenticated Active Directory mapping using LDAP queries and BloodHound.

Identify misconfigurations, high-privileged sessions, and delegation paths to plan a privilege escalation vector to Domain Admin.
---

## 📅 Log Notes: Day 1 — Reconnaissance & Initial Access Verification

### 🛡️ Hardening Verification
We initiated our tests with an unauthenticated status from the Kali Linux attacking platform to check if the Domain Controller was leaking directory data via standard legacy vectors.

1. **SMB Signing Policy Scan:**
   * **Command:** `netexec smb 10.0.2.10`
   * **Result:** `signing:True`
   * **Analysis:** The host enforces cryptographic SMB signing, neutralizing standard unauthenticated relay attacks targeting the DC.

2. **Anonymous Enumeration Testing:**
   * **Commands Executed:** 
     * `netexec smb 10.0.2.10 -u '' -p '' --users` (Null session user sweep)
     * `rpcclient -U "" -N 10.0.2.10 -c "enumdomusers"` (SAMR MSRPC binding)
     * `impacket-samrdump 10.0.2.10` (Low-level SAM dump)
   * **Result:** Initial SMB connection established successfully, but directory object extraction resulted in explicit `NT_STATUS_ACCESS_DENIED` and empty user lists.
   * **Security Conclusion:** Modern Windows Server 2022 default security configurations are active. The DC is well-fortified against unauthenticated intelligence gathering.

### 🔑 Footprint & Foothold Establishment
Following the Assumed Compromise methodology, we shifted focus to authenticated internal assessment. A low-privileged domain user profile was provisioned directly through the Active Directory management console (`dsa.msc`):
* **Target Identity:** `cybercorp.local\s.abdullaev`

We verified domain presence and baseline network authentication from the attacker machine:
```bash
netexec smb 10.0.2.10 -u 's.abdullaev' -p 'Welcome2026!'
texec smb 10.0.2.10 -u 's.abdullaev' -p 'Welcome2026!'
Log Output: [+] cybercorp.local\s.abdullaev:Welcome2026!

Current State: Successful authentication. We hold a non-administrative foothold inside the domain structure.
