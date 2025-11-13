# Project X Enterprise Cyber Lab – Overview

## Purpose of the Lab
The Project X Enterprise Cyber Lab is a fully functional enterprise-style security environment designed to demonstrate hands-on capability in:

- Active Directory administration  
- Windows + Linux domain integration  
- Centralized logging and monitoring  
- IDS/IPS operations  
- SIEM event analysis  
- Endpoint security  
- Attack simulation and defensive detection  
- Dockerized service deployment  
- SOC-style workflows  

This environment mimics a mid-size corporate network and provides a full blue-team and red-team experience.

---

## Lab Components Overview

| Category | System | Purpose |
|---------|--------|---------|
| Domain Services | **Windows Server 2025** | AD DS, DNS, DHCP, IIS for internal apps |
| Windows Endpoint | **Windows 11 Enterprise** | Domain-joined workstation for user simulation |
| Linux Endpoint | **Ubuntu Desktop** | Active Directory–joined Linux client |
| Corporate Server | **Ubuntu Server + Docker** | Mailhog mail server, internal services |
| Security | **Security Onion** | IDS/IPS, PCAP, Zeek, Suricata, SOC tools |
| SIEM | **Wazuh Server** | Log ingestion, agent management, alerting |
| Attacker | **Kali Linux** | Adversary simulation environment |

---

## Lab Goals
1. Build a **multi-OS enterprise network**.  
2. Implement **active monitoring** of endpoints and network events.  
3. Deploy **Dockerized services** (MailHog).  
4. Join both Windows and Linux to AD for **identity-based security**.  
5. Use Wazuh and Security Onion to detect:  
   - Brute force attacks  
   - Lateral movement  
   - Reconnaissance  
   - Suspicious user behavior  
   - Malware-like activity  
6. Develop a **repeatable and defensible architecture** for a cybersecurity portfolio.
