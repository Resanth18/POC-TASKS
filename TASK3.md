# POC-TASKS
# 📌 Linux Security - Exploitation & Hardening (PoC)

This repository demonstrates **firewall and network security misconfigurations** in Linux, including **exploitation and mitigation**.

---

## 🔹 **Task 3: Firewall & Network Security**

### ✅ **Setup: Installing & Configuring a Basic Web Server**

```bash
# Update system and install Apache web server
┌──(kali㉿kali)-[~]
└─$ sudo apt install -y apache2

# Enable Apache to start on boot
┌──(kali㉿kali)-[~]
└─$ sudo systemctl enable apache2

# Start Apache service
┌──(kali㉿kali)-[~]
└─$ sudo systemctl start apache2

# Verify Apache is running
┌──(kali㉿kali)-[~]
└─$ sudo systemctl status apache2 
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/apache2.service; enabled; preset: >
     Active: active (running) since Tue 2025-03-18 11:45:11 IST; 30s ago

# Disable UFW to allow all traffic (Very Insecure!)
┌──(kali㉿kali)-[~]
└─$ sudo ufw disable  
Firewall stopped and disabled on system startup

```
### ✅ **Exploitation: Brute-Force Attack on SSH**

```
# Check open ports on the system
┌──(kali㉿kali)-[~]
└─$ sudo netstat -tulnp

# Scan for open ports using Nmap
┌──(kali㉿kali)-[~]
└─$ sudo nmap -sS -A localhost   

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-03-18 11:49 IST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000094s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.9p1 Debian 3 (protocol 2.0)
| ssh-hostkey: 
|   256 50:f7:e1:64:39:65:bd:ad:c8:1f:43:aa:8b:fc:95:4f (ECDSA)
|_  256 f0:10:c3:71:45:41:da:ce:6e:de:0f:5a:55:88:eb:c9 (ED25519)
80/tcp open  http    Apache httpd 2.4.63 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.63 (Debian)
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6.32
OS details: Linux 2.6.32
Network Distance: 0 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

# Check ports are open
┌──(kali㉿kali)-[~]
└─$ nc -zv localhost 80
localhost [178.0.0.9] 80 (http) open


```
### ✅ **Mitigation: Hardening the System with UFW & iptables**
```
# Enable UFW firewall
┌──(kali㉿kali)-[~]
└─$ sudo ufw enable           
Firewall is active and enabled on system startup

# Allow only SSH (22) and HTTP (80)
┌──(kali㉿kali)-[~]
└─$ sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
Rule added
Rule added (v6)

# Set default UFW rules to block other traffic
┌──(kali㉿kali)-[~]
└─$ sudo ufw default deny incoming
sudo ufw default allow outgoing

Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)

Default outgoing policy changed to 'allow'
(be sure to update your rules accordingly)

# Verify UFW rules
┌──(kali㉿kali)-[~]
└─$ sudo ufw status verbose       
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

# Implement iptables rules to further restrict access
┌──(kali㉿kali)-[~]
└─$ sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Save iptables rules
┌──(kali㉿kali)-[~]
└─$ sudo netfilter-persistent save                    
run-parts: executing /usr/share/netfilter-persistent/plugins.d/15-ip4tables save
run-parts: executing /usr/share/netfilter-persistent/plugins.d/25-ip6tables save
````

## 📌 Task 3 - Summary  

| **Step**              | **Action**                                         | **Command**                                  |
|----------------------|-------------------------------------------------|---------------------------------------------|
| 🔹 **Setup**          | Install and start Apache2 web server            | `sudo apt update && sudo apt install -y apache2`<br>`sudo systemctl enable apache2`<br>`sudo systemctl start apache2` |
|                      | Disable UFW to allow all traffic                 | `sudo ufw disable` |
|                      | Verify Apache2 is running                        | `sudo systemctl status apache2` |
|                      | Check open ports before hardening                | `sudo ss -tulnp` |
| 🔹 **Exploitation**   | Scan for open ports using Nmap                   | `sudo nmap -sS -A localhost` |
|                      | Scan open ports using Netcat                     | `for port in {1..65535}; do (echo >/dev/tcp/127.0.0.1/$port) &>/dev/null && echo "Port $port is open"; done` |
|                      | Identify exposed services                        | `sudo ss -tulnp` |
| 🔹 **Mitigation (UFW)** | Enable UFW and allow only SSH & HTTP            | `sudo ufw enable`<br>`sudo ufw allow 22/tcp`<br>`sudo ufw allow 80/tcp`<br>`sudo ufw default deny incoming` |
|                      | Check UFW status                                 | `sudo ufw status verbose` |
| 🔹 **Mitigation (iptables)** | Allow only SSH & HTTP traffic using iptables | `sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT`<br>`sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT`<br>`sudo iptables -P INPUT DROP` |
|                      | Save iptables rules                              | `sudo iptables-save > /etc/iptables.rules` |
|                      | List iptables rules                              | `sudo iptables -L -v` |
| 🔹 **Verification**   | Re-scan open ports after hardening               | `sudo nmap -sS -A localhost` |
|                      | Check that only SSH (22) and HTTP (80) are open  | `sudo ss -tulnp` |

---
 ### END -x-
