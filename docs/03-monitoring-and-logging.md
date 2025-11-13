# Monitoring & Logging Architecture  
This document describes the complete visibility, telemetry, SIEM, and network monitoring flow across the Project X Enterprise Cyber Lab.

---

# 1. Overview of Logging Components

The lab uses two major monitoring systems:

### ✔ **Wazuh** — Endpoint SIEM  
Collects logs from:
- Windows Server  
- Windows 11 client  
- Ubuntu Desktop  
- Ubuntu Server (Docker host)  

### ✔ **Security Onion** — Network IDS / Full Packet Visibility  
Collects:
- Network metadata (Zeek)  
- IDS alerts (Suricata)  
- Packet captures (Stenographer)  
- Elastic Stack analysis  

Both systems provide full coverage of **endpoint + network** telemetry.

---

# 2. Wazuh SIEM — Endpoint Monitoring

## 2.1 Functions of Wazuh
- Log aggregation (Windows + Linux)
- File integrity monitoring (FIM)
- Sysmon integration
- Threat detection rules
- Vulnerability scanning
- Configuration auditing
- Agent-based monitoring
- Incident correlation

---

## 2.2 Wazuh Agent Deployment

### Agents Installed On:
| System | Log Types Captured | Method |
|--------|---------------------|--------|
| **Windows Server 2025** | Sysmon, Security, System, Application | Wazuh Agent |
| **Windows 11** | Sysmon telemetry, Event logs | Wazuh Agent |
| **Ubuntu Desktop** | auth.log, syslog, sudo events | Wazuh Agent |
| **Ubuntu Server (MailHog)** | Docker logs, syslog | Wazuh Agent |

---

## 2.3 Windows Sysmon Integration

Sysmon enhances visibility with:
- Process creation events  
- File creation logs  
- Network connections  
- Registry changes  
- DLL loads  

These logs → flow into Wazuh → appear in the dashboard for correlation.

---

## 2.4 Wazuh Manager + Indexer

Raw logs travel:
```
Wazuh Agent → Wazuh Manager → Wazuh Indexer (Elasticsearch)
```

Dashboards are accessible at:  
```
https://10.0.0.103:5601
```

---

# 3. Security Onion — Network Monitoring

## 3.1 Tools Enabled

### **Suricata**
- Detects network threats  
- Uses Emerging Threats ruleset  
- Alerts on:  
  - Port scans  
  - Malware C2 traffic  
  - SMB/NTLM events  
  - Brute force attempts  
  - Suspicious payloads  

---

### **Zeek**
Provides metadata for:
- DNS  
- HTTP  
- SMB  
- TLS  
- SSH  
- Files  
- Flows  

Useful for:
- Threat hunting  
- Lateral movement detection  
- Reconnaissance analysis  

---

### **Stenographer**
Captures full PCAP for:
- Forensics  
- Packet-level review  
- Replay analysis  

---

### **Elastic Stack**
Built into Security Onion:
- Kibana  
- Dashboards  
- Query and Hunt interfaces  

---

# 4. Centralized Logging and Monitoring Pipeline

## 4.1 Endpoint Pipeline (Wazuh)

```
Windows + Linux Endpoints
     ↓ (Sysmon + logs)
Wazuh Agent
     ↓ 
Wazuh Manager
     ↓ 
Wazuh Indexer (Elastic)
     ↓ 
Wazuh Dashboard (Kibana)
```

---

## 4.2 Network Pipeline (Security Onion)

```
Network Traffic
     ↓
Suricata (IDS) + Zeek (metadata)
     ↓
Stenographer (PCAP)
     ↓
Elastic Stack
     ↓
Security Onion Dashboards
```

---

## 4.3 Combined SOC Visibility

```
       +-------------------+
       |  Wazuh Dashboard  |
       +-------------------+
             ↑
             |
     Endpoint Telemetry
             |
   (Windows, Linux Hosts)
             |

       +------------------------+
       | Security Onion Console |
       +------------------------+
             ↑
             |
        Network Telemetry
     (Suricata / Zeek / PCAP)
```

The SOC analyst can pivot between both dashboards to correlate host and network events.

---

# 5. Logging & Monitoring Architecture Diagram

Place your generated diagram here:

```markdown
![Logging Architecture](assets/diagrams/logging-architecture.png)
```

This diagram visually represents:
- Endpoint → Wazuh → Elasticsearch flow  
- Network → Security Onion → Dashboards flow  
- Overall SOC architecture  

---

# 6. Use Cases for Detection & Analysis

## ✔ Reconnaissance Detection
- Nmap scans detected by Suricata  
- Zeek connection logs reveal scanning patterns  

## ✔ Brute Force Attacks
- Windows Security Event ID **4625**  
- Wazuh correlation rules flag brute force sequences  

## ✔ Malware Behavior
- Sysmon process creation  
- Suspicious command-line arguments  
- Sysmon network events  

## ✔ Phishing Simulation
- SMTP events captured by MailHog  
- Email analysis possible in Wazuh  

## ✔ Lateral Movement
- SMB logs  
- Failed logons  
- NTLM anomalies  

---

# 7. Log Validation Checklist

| Component | Verification Step |
|----------|-------------------|
| Wazuh Agent | Appears **Active** in dashboard |
| Sysmon | Events appear under Windows logs |
| Suricata Alerts | Visible in Security Onion → Alerts |
| Zeek Logs | DNS/HTTP/Conn logs populate correctly |
| PCAP | Stenographer captures traffic |
| Combined View | Both dashboards visualize activity |


