# 📌 Linux Security - Exploitation & Hardening (PoC)

This repository demonstrates **log analysis & intrusion detection** in Linux, including **exploitation and mitigation**.

---

## 🔹 **Task 6: Log Analysis & Intrusion Detection**

### ✅ **Step 1: Enable System Logging**
Enable logging to track authentication events.

```bash
# Ensure logging services are enabled
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo systemctl enable systemd-journald
sudo systemctl restart systemd-journald

# Verify logging is active
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo journalctl --no-pager -n 20
```
### ✅ Step 2: Simulate Multiple Failed SSH Login Attempts
```
# Attempt SSH login with incorrect password (Simulated attack)
┌──(kali㉿kali)-[~/Desktop]
└─$ ssh root@localhost
# Enter wrong password multiple times to trigger logs

```
### ✅ Step 3: Analyze Logs for Failed Logins
```
# View failed login attempts
┌──(kali㉿kali)-[~/Desktop]
└─$ grep "Failed password" /var/log/auth.log

# Alternative method (for systems using journalctl)
┌──(kali㉿kali)-[~/Desktop]
└─$ journalctl -u sshd --no-pager | grep "Failed password"
Mar 17 14:05:21 kali sshd[1234]: Failed password for root from 192.168.1.100 port 51234 ssh2
Mar 17 14:05:23 kali sshd[1234]: Failed password for root from 192.168.1.100 port 51235 ssh2
```
### ✅ Step 4: Detect Brute-Force Attempts
```
# Count failed logins per IP
┌──(kali㉿kali)-[~/Desktop]
└─$ grep "Failed password" /var/log/auth.log | awk '{print $NF}' | sort | uniq -c | sort -nr
```
### ✅ Step 5: Mitigation - Install fail2ban
```
# Install fail2ban
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo apt install fail2ban -y

# Enable fail2ban service
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```
### ✅ Step 6: Configure fail2ban
```
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo fail2ban-client status sshd

[sshd]
enabled = true
bantime = 600
maxretry = 3
```
### ✅ Step 7: Restart fail2ban
```
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo systemctl restart fail2ban
```
### ✅ Step 8: Verify fail2ban is Working
```
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 3
|  |- Total failed: 10
|  `- File list: /var/log/auth.log
`- Actions
   |- Currently banned: 1
   |- Total banned: 3
   `- Banned IP list: 192.168.1.100
```
### ✅ Step 9: Automate Log Monitoring
```
# Install logwatch
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo apt install logwatch -y

# Run logwatch to analyze logs
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo logwatch --detail high --logfile /var/log/auth.log --range today
```
### ✅ Step 10: Review Logs Regularly
```
# Watch log file in real-time
┌──(kali㉿kali)-[~/Desktop]
└─$ tail -f /var/log/auth.log

```
### Summary
## 📌 Task 6: Summary Table

| **Step**  | **Description** | **Commands / Code Snippets** |
|-----------|---------------|-----------------------------|
| **1️⃣ Enable System Logging** | Ensure logging is active. | `sudo systemctl enable systemd-journald` <br> `sudo systemctl restart systemd-journald` |
| **2️⃣ Simulate Failed SSH Logins** | Use incorrect passwords to generate logs. | `ssh root@localhost` (enter wrong password multiple times) |
| **3️⃣ Analyze Logs for Attacks** | Find failed login attempts in logs. | `grep "Failed password" /var/log/auth.log` <br> `journalctl -u sshd --no-pager | grep "Failed password"` |
| **4️⃣ Detect Brute-Force Attempts** | Count repeated failed login attempts. | `grep "Failed password" /var/log/auth.log | awk '{print $NF}' | sort | uniq -c | sort -nr` |
| **5️⃣ Mitigation - Install fail2ban** | Block repeated failed attempts. | `sudo apt install fail2ban -y` |
| **6️⃣ Configure fail2ban** | Set SSH protection rules. | `sudo nano /etc/fail2ban/jail.local` <br> Add: <br> `[sshd]` <br> `enabled = true` <br> `bantime = 600` <br> `maxretry = 3` |
| **7️⃣ Restart fail2ban** | Apply new rules. | `sudo systemctl restart fail2ban` |
| **8️⃣ Check fail2ban Status** | Verify if fail2ban is blocking attackers. | `sudo fail2ban-client status sshd` |
| **9️⃣ Automate Log Monitoring** | Install logwatch for automated reporting. | `sudo apt install logwatch -y` <br> `sudo logwatch --detail high --logfile /var/log/auth.log --range today` |
| **🔟 Review Logs Regularly** | Monitor logs for ongoing attacks. | `tail -f /var/log/auth.log` |

### END-x-
