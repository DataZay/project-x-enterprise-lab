```markdown
# 07 – Wazuh Setup (Ubuntu) & Windows Agent

## 1. Wazuh Server Install on Ubuntu

On your chosen Ubuntu host (can be `corp-svr` or a dedicated Ubuntu VM):

## 1. Ensure `curl` is installed:

```bash
sudo apt update
sudo apt install curl
Run the Wazuh installer with minimum requirement check ignored:

bash
Copy code
curl -sO https://packages.wazuh.com/4.9/wazuh-install.sh
sudo bash ./wazuh-install.sh -a -i
Wait for the installation to complete.

Note the generated credentials shown by the installer.

## 2. Log In to Wazuh Dashboard
On the Wazuh server:

Open a browser and go to: https://localhost

Click Advanced… → Accept the Risk and Continue.

Log in with the credentials from the installer.

## 3. Install Wazuh Agent on Windows Client
On project-x-win-client:

Download the Wazuh agent:

Open Edge and go to:

https://packages.wazuh.com/4.x/windows/wazuhagent-4.9.2-1.msi

Run the MSI:

Accept the license.

When prompted, provide:

Wazuh server IP (e.g., 10.0.0.10 or whichever host runs Wazuh).

Agent name (e.g., project-x-win-client).

Registration key if required (generated on server via manage_agents).

## 4. Register Agent on Wazuh Server
On the Wazuh Ubuntu server:

bash
Copy code
sudo su -
/var/ossec/bin/manage_agents
# A = Add agent
# Provide a name that matches your client
# When prompted, note the agent key (E = extract key)
Copy the agent key.

Back on Windows, paste in the agent key where required (depending on the installer mode), or add it to the agent config.

## 5. Start Wazuh Agent on Windows
Open PowerShell as Administrator:

powershell
Copy code
NET START WAZUH

## 6. Validate
In Wazuh dashboard, verify that the agent appears as connected.

Generate test events on the Windows client (e.g., failed logins), then check Wazuh dashboards for alerts.
