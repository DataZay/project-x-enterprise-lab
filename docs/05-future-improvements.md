# Future Improvements & Expansion Roadmap
This document outlines recommended enhancements, expansions, and long-term architectural improvements to grow the Project X Enterprise Cyber Lab into a full-scale SOC training and enterprise environment.

---

# 1. Network Architecture Enhancements

## 1.1 Add pfSense Firewall
Introduce segmentation, NAT rules, and IDS-inline testing:
- DMZ for MailHog or web services  
- LAN for AD + clients  
- Security VLAN  
- Attacker VLAN  

Benefits:
- Realistic enterprise segmentation  
- Rule-based traffic control  
- Improved SOC visibility  

---

## 1.2 Implement VLAN Segregation
Using managed switches or virtual VLANs:
- VLAN 10 → Domain Services  
- VLAN 20 → Clients  
- VLAN 30 → Security Tools  
- VLAN 40 → DMZ  
- VLAN 50 → Attackers  

---

# 2. Monitoring & Detection Improvements

## 2.1 Add Elastic Agent Fleet
Unified logging and agent management across:
- Windows  
- Linux  
- Docker containers  

Benefits:
- More data sources  
- Easier agent deployment  
- Built-in dashboards  

---

## 2.2 Implement Sysmon on All Windows Systems
Enhances deep endpoint telemetry.

---

## 2.3 Add Sigma Ruleset
Integrate Sigma → Wazuh → Elasticsearch for:
- More detection coverage  
- Custom rules writing  

---

# 3. Automation & Infrastructure as Code

## 3.1 Add Ansible / Terraform Automation
Automate deployment of:
- VMs  
- Users  
- Group policies  
- Wazuh agents  
- Docker services  

Benefits:
- Repeatability  
- Version-controlled infrastructure  

---

## 3.2 CI/CD Pipeline for Detection Rules
Use GitHub Actions to:
- Validate new Wazuh rules  
- Test Suricata rule updates  
- Push updates to lab automatically  

---

# 4. Additional Security Tools to Add

### ✔ Honeypots (Cowrie, T-Pot)
Capture attacker activity.

### ✔ Velociraptor
DFIR agent for live forensics.

### ✔ TheHive + Cortex
Incident response case management.

### ✔ GVM (OpenVAS)
Full vulnerability scanning platform.

### ✔ Caldera (MITRE ATT&CK automation)
Simulate attacker TTPs.

---

# 5. SOC Analyst Capabilities Expansion

## 5.1 Playbook Development
Develop:
- Malware investigation playbooks  
- Alert triage workflows  
- Escalation procedures  

---

## 5.2 Blue Team Dashboards
Build:
- User login anomaly dashboards  
- Network lateral movement dashboards  
- Web server anomaly visualizations  

---

# 6. Long-Term Vision
Transform Project X into a **complete SOC learning ecosystem** with:

- Realistic enterprise segmentation  
- Endpoint + network telemetry  
- Automated attack simulation  
- Documented SOC procedures  
- Custom alerting  
- DFIR capabilities  
- Threat intelligence ingestion  
- Vulnerability management  
- Case- and incident-driven workflows  

---

# Future Enhancements Complete
This roadmap provides a structured plan for scaling the Project X Enterprise Cyber Lab into a fully operational SOC training platform.
