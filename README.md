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
