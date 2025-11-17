# Cyber Academy – Project-X Enterprise Lab

A hands-on, enterprise-style virtual environment designed for cybersecurity training and portfolio building.

This lab simulates a small corporate network with:

- **Windows Server 2025 Domain Controller** (AD DS + DNS + DHCP + IIS + File Services)
- **Windows 11 Enterprise workstation** joined to the domain
- **Ubuntu Desktop Linux workstation** joined to the domain
- **Ubuntu “Corp Server”** running Docker and MailHog
- **Security Onion** sensor / analyst workstation
- **Wazuh** SIEM server + Windows agent

All hosts live in a common **VirtualBox NAT Network** (`project-x-nat`), providing Internet egress and centralized AD authentication.

---

## 1. Objectives

- Practice **enterprise Windows and Linux administration**.
- Build and manage **Active Directory**, DNS, and DHCP.
- Configure **Linux domain joins** using Winbind and Kerberos.
- Run **Dockerized MailHog** as a lab mail server and build small scripts that interact with it.
- Deploy **Security Onion** and **Wazuh** to monitor the environment.
- Generate realistic lab scenarios for SOC / blue-team practice.

---

## 2. Lab Topology

### 2.1 Virtual Network (VirtualBox NAT Network)

- **Network name:** `project-x-nat`
- **IP range:** `10.0.0.0/24`
- **Gateway (VirtualBox NAT):** `10.0.0.1`
- **DHCP dynamic range:** `10.0.0.100 – 10.0.0.200`

### 2.2 Hosts

| Hostname                | IP Address       | Role / Function                        |
|-------------------------|------------------|----------------------------------------|
| `project-x-dc`          | `10.0.0.5`       | Windows Server 2025 DC, DNS, DHCP, IIS |
| `project-x-win-client`  | `10.0.0.100`     | Windows 11 Enterprise workstation      |
| `project-x-linux-client`| `10.0.0.101`     | Ubuntu Desktop client (domain joined)  |
| `project-x-corp-svr`    | `10.0.0.8`       | Ubuntu server with Docker / MailHog    |
| `project-x-sec-work`    | `10.0.0.103`     | Security Onion analyst box             |
| `project-x-sec-box`     | `10.0.0.10`      | Ubuntu security box / tools            |
| `project-x-attacker`    | DHCP (dynamic)   | Kali Linux attacker                    |

> You can adjust some addresses if needed, but keep the **DC = 10.0.0.5** and **gateway = 10.0.0.1** consistent across docs.

### 2.3 Lab Accounts (Demo Passwords)

_These are intentionally weak and for lab only. Do not reuse in real environments._

| Account                          | Host / Scope            | Password         |
|----------------------------------|-------------------------|------------------|
| `Administrator`                  | `project-x-dc`          | `@Password!`     |
| `johnd@corp.local`              | Win client user         | `@Password123!`  |
| `janed`                          | Linux client user       | `@Password123!`  |
| `project-x-sec-work`            | Security Onion          | `@Password123!`  |
| `sec-work@sec-box`              | Security box            | `@Password123!`  |
| `project-x-admin`               | Corp server admin       | `@Password123!`  |
| `attacker@attacker` (Kali)      | Kali                     | `kali` / `Kali2025!` |

Customize these in a real deployment.

---

## 3. Repository Layout

- `docs/` – All step-by-step lab build guides:
  - **00** – Lab overview and prerequisites
  - **01** – Network & architecture
  - **02** – Windows Server 2025 DC build
  - **03** – Windows 11 client setup & domain join
  - **04** – Ubuntu Desktop domain join
  - **05** – Corp server, Docker & MailHog
  - **06** – Security Onion setup
  - **07** – Wazuh install & Windows agent
  - **08** – Sample lab scenarios (SOC / blue-team exercises)

- `configs/` – Reusable config snippets:
  - `docker/mailhog-docker-compose.yml`
  - `samba/smb.conf.example`
  - `wazuh/notes-agent-config.md`

- `scripts/` – Helper scripts:
  - `mailhog/test_message.py` – Sends a test email via MailHog
  - `mailhog/email_poller.sh` – Polls MailHog for new mail to `janed`
  - `helpers/validate-connectivity.md` – Simple validation commands

---

## 4. Getting Started

### 4.1 Prerequisites

- Virtualization:
  - Oracle VirtualBox _or_ VMware Workstation Pro
- ISOs:
  - Windows Server 2025 evaluation
  - Windows 11 Enterprise
  - Ubuntu Desktop 22.04 LTS
  - Ubuntu Server 22.04 LTS (optional for corp server)
  - Security Onion ISO
  - Kali Linux 2024.x

### 4.2 High-Level Build Order

1. Configure VirtualBox NAT Network: `project-x-nat`, `10.0.0.0/24`.
2. Build **`project-x-dc`** and promote to **CORP.local** DC with DNS + DHCP + IIS.
3. Build **`project-x-win-client`** and join it to `corp.local`.
4. Build **`project-x-linux-client`** and join it to `CORP` AD using Winbind & Kerberos.
5. Build **`project-x-corp-svr`**, install Docker, and deploy MailHog.
6. Build **`project-x-sec-work`** with Security Onion.
7. Install **Wazuh** on Ubuntu and deploy the Windows agent on the Win client.
8. Run the **sample lab scenarios** in `docs/08-sample-lab-scenarios.md`.

Each step has its own detailed guide under `docs/`.

---

## 5. How to Use This for Your Portfolio

In your resume / LinkedIn / GitHub:

- Mention you:
  - Designed and deployed a multi-VM enterprise lab.
  - Implemented AD DS, DNS, DHCP & Group Policy.
  - Joined Linux clients to AD using Winbind & Kerberos.
  - Deployed Docker-based MailHog and scripted email tests.
  - Configured Security Onion and Wazuh for monitoring.
- Link this repository so recruiters can see:
  - Architecture diagrams (optional to add later).
  - Detailed build runbooks.
  - Supporting scripts and configuration.

---

## 6. Contributing / Extending

Ideas to grow the lab:

- Add **GPOs** for hardening and logging.
- Add **file shares** and **NTFS permissions** for access control practice.
- Add more **attack & detection** labs (phishing, malicious PowerShell, etc.).
- Integrate other tools (e.g., ELK, Zeek custom rules, etc.).

Feel free to fork and customize this to your own style.

