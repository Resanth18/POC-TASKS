# 📌 Linux Security - Exploitation & Hardening (PoC)

This repository demonstrates **security auditing automation** in Linux, including **exploitation and mitigation**.

---

## 🔹 Task 5: Automated Security Auditing & Scripting

### ✅ Overview
This task involves writing a **Bash script** that:
- **Monitors user login attempts** (`last`, `auth.log`)
- **Detects running services** (`systemctl list-units --type=service`)
- **Checks disk usage** (`df -h`)
- **Triggers alerts on unauthorized SSH login attempts**

---

## ✅ Step 1: Create the Security Audit Script

### **1️⃣ Create the Bash Script**
Run the following command to create a script file:

```bash
┌──(kali㉿kali)-[~]
└─$ nano security_audit.sh

## Copy paste the following in NANO text editor
{ #!/bin/bash
# Log files
LOG_FILE="/var/log/auth.log"  # On some systems, use /var/log/secure
ALERT_EMAIL="your_email@example.com"

# Check user login attempts
echo "====== User Login Attempts ======"
last -n 10
echo ""

# Check for unauthorized SSH access
echo "====== Unauthorized SSH Attempts ======"
grep "Failed password" $LOG_FILE | tail -n 10
echo ""

# List running services
echo "====== Running Services ======"
systemctl list-units --type=service --state=running | tail -n 20
echo ""

# Monitor disk usage
echo "====== Disk Usage ======"
df -h | grep "^/dev"
echo ""

# Send an alert if unauthorized SSH access is found
if grep -q "Failed password" $LOG_FILE; then
    echo "[ALERT] Unauthorized SSH Login Attempt Detected!" | mail -s "Security Alert" $ALERT_EMAIL
fi }

```
## ✅ Step 2: Make the Script Executable & Run the Script

```
┌──(kali㉿kali)-[~]
└─$ ./security_audit.sh

====== User Login Attempts ======
username    pts/0        192.168.1.5    Mon Mar 18 10:45 - 10:50  (00:05)
...

====== Unauthorized SSH Attempts ======
Mar 18 11:10:21 kali sshd[1234]: Failed password for root from 192.168.1.10 port 5402 ssh2
...

====== Running Services ======
  apache2.service             loaded active running The Apache HTTP Server
  ssh.service                 loaded active running OpenSSH server daemon
...

====== Disk Usage ======
/dev/sda1        50G  20G  30G  40% /
...
```
## ✅ Step 3: Automate the Script Using Cron & Verify the Cron Job is Running

### **Open the Cron Job Editor & Add the Following Line at the Bottom
```
┌──(kali㉿kali)-[~]
└─$ crontab -e

*/10 * * * * /path/to/security_audit.sh >> /var/log/security_audit.log 2>&1
```
### **✅ This runs the script every 10 minutes and logs output to /var/log/security_audit.log
```
┌──(kali㉿kali)-[~]
└─$ crontab -l
cat /var/log/security_audit.log
```
### **Summary
## 📌 Task 5: Summary Table

| **Step**  | **Description** | **Commands / Code Snippets** |
|-----------|---------------|-----------------------------|
| **1️⃣ Create the Script** | Create a Bash script to monitor security logs, services, and disk usage. | `nano security_audit.sh` |
| **2️⃣ Add Security Checks** | Script includes: user logins, unauthorized SSH attempts, running services, and disk usage. | See full script in README.md |
| **3️⃣ Make the Script Executable** | Grants execution permission. | `chmod +x security_audit.sh` |
| **4️⃣ Run the Script** | Executes the script manually. | `./security_audit.sh` |
| **5️⃣ Automate with Cron** | Runs the script every 10 minutes. | `crontab -e` <br> `*/10 * * * * /path/to/security_audit.sh >> /var/log/security_audit.log 2>&1` |
| **6️⃣ Check Cron Job Output** | Verifies if logs are being generated. | `cat /var/log/security_audit.log` |
| **7️⃣ Exploitation** | Identifies failed logins, weak passwords, and misconfigurations. | **Analyzing script output** |
| **8️⃣ Mitigation** | Secures system by disabling unused accounts, enforcing strong passwords, and restricting SSH root login. | `sudo userdel old_user` <br> `sudo passwd -l weak_user` <br> Edit `/etc/ssh/sshd_config`: `PermitRootLogin no` |

### ** END-x-
