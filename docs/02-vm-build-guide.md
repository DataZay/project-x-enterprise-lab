# VM Build & Configuration Guide
This document provides a complete, detailed walkthrough for building every virtual machine in the Project X Enterprise Cyber Lab. These steps ensure consistent deployment across Windows, Linux, Docker-based services, and security monitoring platforms.

---

# 1. Windows Server 2025 — Domain Controller (AD/DNS/DHCP/IIS)

## 1.1 Install Windows Server 2025
1. Create a new VirtualBox/VMWare VM.
2. Assign:
   - **4–8 GB RAM**
   - **2+ CPU cores**
   - **60 GB virtual disk**
3. Install Windows Server 2025 Standard (Desktop Experience).
4. Set local Administrator password.
5. Set hostname:

```
project-x-dc
```

## 1.2 Configure Networking (Static IP)
Open **Settings → Network & Internet → Ethernet → Edit IP Settings** and configure:

- **IP:** `10.0.0.5`
- **Subnet:** `255.255.255.0`
- **Gateway:** `10.0.0.1`
- **DNS:** `10.0.0.5` (self)

Restart networking or reboot the server.

---

## 1.3 Install Active Directory Domain Services (AD DS)
1. Open **Server Manager**
2. Click **Add Roles and Features**
3. Role-Based installation → select local server
4. Under Server Roles → check:
   - **Active Directory Domain Services**
5. Install → when done, click:
   - **Promote this server to a domain controller**

### Domain Setup
- Select **Add a new forest**
- Domain Name:  
  ```
  projectx.local
  ```
- NetBIOS Name:  
  ```
  PROJECTX
  ```

Accept defaults → reboot after installation.

---

## 1.4 Configure DNS
DNS installs automatically with AD.

### Validate DNS Zones
1. Open **DNS Manager**
2. Confirm:
   - Forward Lookup Zone → `projectx.local`
   - Reverse Lookup Zone → `10.0.0.x` (create if missing)

### Add Host Records (Optional)
Add A or PTR records for endpoints if needed.

---

## 1.5 Install and Configure DHCP
1. Server Manager → Add Roles → **DHCP Server**
2. Post-install wizard → **Complete DHCP configuration**
3. Open DHCP MMC → configure:

### Create IPv4 Scope
- Name: `ProjectX Scope`
- Start IP: `10.0.0.50`
- End IP: `10.0.0.200`
- Subnet Mask: `255.255.255.0`
- Router: *blank or 10.0.0.1*
- DNS: `10.0.0.5`
- Lease duration: default

### Authorize DHCP
- Right-click server → **Authorize**

Your server now handles DHCP for your lab.

---

## 1.6 Install IIS (Internal Web Server)

### Install Web Server Role
1. Open **Server Manager**
2. Add Roles → **Web Server (IIS)**
3. Accept default settings → Install

### Test IIS locally
Open a browser:

```
http://localhost
```

You should see the default IIS landing page.

### Customize IIS Home Page
Navigate to:

```
C:\inetpub\wwwroot\
```

Edit `index.html`:

```html
<h1>Project X - Internal Web Server</h1>
<p>Welcome to the lab environment. IIS is functioning normally.</p>
```

### Test from another machine
From any endpoint browser:

```
http://10.0.0.5
```

---

# 2. Windows 11 Enterprise Client

## 2.1 Install Windows 11
- Configure VM with **4 GB RAM**, **2 CPUs**, **50 GB disk**
- Install Windows 11 Enterprise
- Set hostname:
  ```
  project-x-win-client
  ```

## 2.2 Set Static IP
Network Adapter → IPv4:

- IP: `10.0.0.100`
- Subnet: `255.255.255.0`
- DNS: `10.0.0.5`

## 2.3 Join Domain
1. Settings → System → About → Domain Join
2. Enter domain:
   ```
   projectx.local
   ```
3. Credentials:
   - Username: `Administrator`
4. Reboot
5. Log in as:
   ```
   PROJECTX\Administrator
   ```

---

# 3. Ubuntu Desktop — Linux AD Client

## 3.1 Install Ubuntu Desktop
- VM: **4 GB RAM**, **2 CPU**, **40 GB disk**
- Hostname:
  ```
  project-x-linux-client
  ```
- Static IP: `10.0.0.101`

## 3.2 Install AD Integration Tools

```bash
sudo apt update
sudo apt install realmd sssd sssd-tools adcli samba-common packagekit oddjob oddjob-mkhomedir samba-libs -y
```

## 3.3 Discover Domain

```bash
realm discover projectx.local
```

## 3.4 Join Domain

```bash
sudo realm join projectx.local -U Administrator
```

## 3.5 Allow Domain Logins
Edit `/etc/sssd/sssd.conf` only if needed.

## 3.6 Verify

```bash
id administrator@projectx.local
```

---

# 4. Ubuntu Server — MailHog (Docker-Based Corporate Server)

## 4.1 Install Ubuntu Server
- Hostname:
  ```
  corp-svr
  ```
- Static IP: `10.0.0.8`

## 4.2 Install Docker & Compose

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
```

## 4.3 Deploy MailHog

```bash
mkdir /opt/mailhog
cd /opt/mailhog
```

Create `docker-compose.yml`:

```yaml
version: "3"
services:
  mailhog:
    image: mailhog/mailhog
    container_name: mailhog
    restart: unless-stopped
    ports:
      - "1025:1025"
      - "8025:8025"
```

Start service:

```bash
sudo docker-compose up -d
```

### Access MailHog
- SMTP: `10.0.0.8:1025`
- Web UI: `http://10.0.0.8:8025`

---

# 5. Security Onion — Network IDS / SOC Suite

## 5.1 Install Security Onion
- Import OVA or install ISO.
- VM Specs: **8 GB RAM**, **4 CPU cores**, **100 GB disk**
- Hostname:
  ```
  project-x-sec-box
  ```
- Static IP: `10.0.0.10`

## 5.2 Setup Mode
Choose:

- **Evaluation Mode**
- Single-node deployment

## 5.3 Tools Enabled
- **Suricata** (IDS/IPS)
- **Zeek** (network metadata)
- **Stenographer** (full PCAP)
- **Elastic Stack** (Kibana dashboards)

## 5.4 Verify Sensor Health
Check:
- Alerts under SOC → Alerts
- Zeek logs under Hunt
- Packet captures in PCAP

---

# 6. Wazuh Server — SIEM Platform

## 6.1 Install Wazuh
- Deploy OVA file
- VM Specs: **8 GB RAM**, **4 CPU**, **120 GB disk**
- Hostname:
  ```
  project-x-sec-work
  ```
- IP:
  ```
  10.0.0.103
  ```

## 6.2 Access Dashboard
In browser:

```
https://10.0.0.103:5601
```

Log in with default credentials (then change them).

## 6.3 Add Agents
Install Wazuh Agent on:
- Windows Server
- Windows 11 Client
- Ubuntu Desktop
- Ubuntu Corporate Server

Verify:
- Agent status: **Active**
- Logs flowing

---

# 7. Kali Linux — Attacker VM

## 7.1 Install Kali Linux
- Hostname:
  ```
  attacker-kali
  ```
- IP: DHCP or static `10.0.0.150`

## 7.2 Update Packages

```bash
sudo apt update && sudo apt upgrade -y
```

## 7.3 Install Tools

```bash
sudo apt install nmap hydra netcat-traditional impacket-scripts enum4linux metasploit-framework -y
```

---

# 8. Validation Checklist

| Task | Verified By |
|------|-------------|
| AD Domain functional | Clients authenticate domain users |
| DNS resolution works | Ping by hostname |
| DHCP working | Windows/Linux clients receive leases |
| IIS functional | Access http://10.0.0.5 |
| MailHog running | Web UI loads |
| Wazuh operational | Agents appear active |
| Security Onion detects activity | Suricata fires on port scans |
| Kali communicates with network | Ping + Nmap successful |

---

# Build Guide Complete
You may now proceed to **docs/03-monitoring-and-logging.md** for the visibility stack.
