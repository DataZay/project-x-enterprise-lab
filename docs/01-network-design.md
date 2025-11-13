# Enterprise Network Design

## Network Overview Diagram

<img width="1536" height="1024" alt="Network Overview" src="https://github.com/user-attachments/assets/3a46d566-0e9a-4748-94a0-6bf47987c49b" />

---

## Logical Network Layout

The lab uses a unified internal NAT network (`10.0.0.0/24`).  
Each VM receives a static IP for consistency.

| VM | Hostname | IP | Purpose |
|----|----------|----|---------|
| Windows Server 2025 | project-x-dc | **10.0.0.5** | AD DS, DNS, DHCP, IIS |
| Windows 11 Client | project-x-win-client | **10.0.0.100** | Domain workstation |
| Ubuntu Desktop | project-x-linux-client | **10.0.0.101** | Linux AD client |
| Ubuntu Corporate Server | corp-svr | **10.0.0.8** | Docker services |
| Security Onion | project-x-sec-box | **10.0.0.10** | IDS/IPS + SOC tools |
| Wazuh Workstation | project-x-sec-work | **10.0.0.103** | Wazuh dashboard |
| Kali Attacker | attacker-kali | DHCP or **10.0.0.150** | Offensive testing |

---

## Domain Configuration

- **Domain Name:** `projectx.local`  
- **NetBIOS Name:** `PROJECTX`  
- **DHCP Scope:**  
  - Range: `10.0.0.50 - 10.0.0.200`  
  - Gateway: `10.0.0.5` (DC)  
  - DNS: `10.0.0.5`

---

## Firewall & Segmentation (Internal Logical Only)

Although no pfSense router is used here, segmentation is simulated through host-only boundaries and Security Onion inspection.

---

## Diagram Notes

<img width="1536" height="1024" alt="Diagram" src="https://github.com/user-attachments/assets/7781134e-60fe-4e26-92c5-b27382a7cce3" />


Recommended diagram tools:
- draw.io  
- Lucidchart  
- Excalidraw  
- Microsoft Visio  


