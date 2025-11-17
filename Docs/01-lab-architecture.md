# 01 – Lab Architecture & Network Plan

## 1. Virtual Network

Configure a NAT Network in VirtualBox:

- **Name:** `project-x-nat`
- **CIDR:** `10.0.0.0/24`
- **Gateway:** `10.0.0.1`
- **DHCP Range:** `10.0.0.100 – 10.0.0.200`

Attach all VMs to this NAT Network on Adapter 1.

## 2. Host Inventory

| VM Name               | OS                      | vCPU | RAM    | Disk  | IP Address   | Notes                              |
|-----------------------|------------------------|------|--------|-------|--------------|------------------------------------|
| `project-x-dc`        | Windows Server 2025    | 2    | 4096MB | 50GB  | 10.0.0.5     | DC, DNS, DHCP, IIS                 |
| `project-x-win-client`| Windows 11 Enterprise  | 2    | 4096MB | 80GB  | 10.0.0.100   | Domain client                      |
| `project-x-linux-client` | Ubuntu 22.04 Desktop| 1    | 2048MB | 80GB  | 10.0.0.101   | Domain Linux client                |
| `project-x-sec-work`  | Security Onion         | 1    | 2048MB | 55GB  | 10.0.0.103   | Security analyst workstation       |
| `project-x-sec-box`   | Ubuntu 22.04 Desktop   | 2    | 4096MB | 80GB  | 10.0.0.10    | Security tools                     |
| `project-x-corp-svr`  | Ubuntu Server/Desktop  | 1    | 2048MB | 25GB  | 10.0.0.8     | Docker + MailHog                   |
| `project-x-attacker`  | Kali Linux 2024.2      | 1    | 2048MB | 55GB  | DHCP (dynamic)| Attacker box                       |

## 3. Domain Information

- **Domain Name:** `corp.local`
- **NetBIOS Name:** `CORP`
- **Domain Controller:** `project-x-dc` (`10.0.0.5`)

## 4. IP and DNS Settings Summary

All members should use:

- **Default Gateway:** `10.0.0.1`
- **DNS Server (domain members):** `10.0.0.5`
- **Subnet Mask:** `255.255.255.0`

Detailed per-VM networking is covered in the step-by-step guides.
