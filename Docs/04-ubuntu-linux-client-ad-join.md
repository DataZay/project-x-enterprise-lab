```markdown
# 04 – Ubuntu Desktop Linux Client AD Join (`project-x-linux-client`)

(Condensed but faithful version of your Winbind/Kerberos steps.)

## 1. Install Ubuntu Desktop

1. Create VM:
   - Name: `project-x-linux-client`
   - OS: Ubuntu 22.04 Desktop
   - vCPU: 1
   - RAM: 2048 MB
   - Disk: 80 GB
   - Network: Adapter 1 → `project-x-nat`
2. Install Ubuntu:
   - Choose keyboard layout.
   - Normal installation, default options.
   - Erase disk and install Ubuntu.
   - Create local user `janed` (password: `@Password123!`).
   - Disable Location Services.

## 2. Static IP and DNS

1. Open **Settings → Network**.
2. Click the gear on the wired connection → IPv4.
3. Set **Manual**:

   - Address: `10.0.0.101`
   - Netmask: `255.255.255.0`
   - Gateway: `10.0.0.1`
   - DNS: `10.0.0.5`

4. Save and reconnect.

Validate connectivity:

```bash
ping -c4 10.0.0.5
ping -c4 corp.local
3. Install Winbind, Kerberos, and Samba Modules
bash
Copy code
sudo apt update
sudo apt -y install winbind libpam-winbind libnss-winbind krb5-config samba-dsdb-modules samba-vfs-modules
During krb5-config, set:

Default Kerberos version 5 realm: CORP.PROJECT-X-DC.COM

Kerberos servers: CORP.PROJECT-X-DC.COM

Administrative server: CORP.PROJECT-X-DC.COM

## 4. Configure smb.conf
Backup and create a new Samba config:

sudo mv /etc/samba/smb.conf /etc/samba/smb.conf.org
sudo nano /etc/samba/smb.conf
Paste the content from configs/samba/smb.conf.example, then save.
bash
Copy code (From server-world info winbind)

[global]
kerberos method = secrets and keytab
realm = CORP.PROJECT-X-DC.COM
workgroup = CORP
security = ads
template shell = /bin/bash
winbind enum groups = Yes
winbind enum users = Yes
winbind separator = +
idmap config * : rangesize = 1000000
idmap config * : range = 1000000-19999999
idmap config * : backend = autorid

Exit Nano with CTRL + X, then Y key, Enter Key.

## 5. Configure nsswitch.conf
bash
Copy code
sudo nano /etc/nsswitch.conf
Ensure these lines include winbind:
text
Copy code
passwd:         files systemd winbind
group:          files systemd winbind

## 6. PAM – Home Directory Creation
bash
Copy code
sudo pam-auth-update
Check “Create home directory on login”.

Use TAB to select OK, then Enter.

## 7. DNS Resolver
bash
Copy code
sudo nano /etc/resolv.conf
Configure:

text
Copy code
nameserver 10.0.0.5
nameserver 127.0.0.53
options edns0 trust-ad
search .

## 8. Join Domain
bash
Copy code
sudo net ads join -U Administrator
# enter Administrator password
sudo systemctl restart winbind
net ads info
wbinfo -u
You should see domain user accounts listed.

## 9. Test Domain Login
Switch to a TTY or terminal:

bash
Copy code
sudo login
Log in as:

Username: CORP+janed

Password: @Password123!

Run:

bash
Copy code
id
You should see a domain user with UID/GIDs mapped.

Finally, on the DC in ADUC, you should see the Linux client computer object under Computers.
