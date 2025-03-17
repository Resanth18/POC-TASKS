# POC-TASKS
# 📌 Linux Security - Exploitation & Hardening (PoC)

This repository demonstrates **user and permission misconfigurations** in Linux, including **exploitation and mitigation**.

---

## 🔹 **Task 1: User & Permission Misconfigurations**

### ✅ **Setup: Creating Users & Misconfiguring Permissions**

```bash
# Creating user rcb
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo useradd rcb

# Set passwords for the users
┌──(kali㉿kali)-[~/Desktop]
└─$ echo "rcb:1234" | sudo chpasswd

# Verify users exist
cat /etc/passwd | grep "rcb"

# Check default permissions of sensitive files
┌──(kali㉿kali)-[~/Desktop]
└─$ ls -l /etc/shadow
-rw-r----- 1 root shadow 1722 Mar 17 19:11 /etc/shadow

# Assign incorrect permissions to /etc/shadow (Very Dangerous!)
┌──(kali㉿kali)-[~/Desktop]
└─$ sudo chmod 777 /etc/shadow

# Verify permissions have changed
┌──(kali㉿kali)-[~/Desktop]
└─$ ls -l /etc/shadow         
-rwxrwxrwx 1 root shadow 1722 Mar 17 19:11 /etc/shadow
```

---

### ✅ **Exploitation: Accessing Sensitive Files as a Low-Privileged User**

```bash
# Switch to rcb (a low-privileged user)
┌──(kali㉿kali)-[~]
└─$ su - rcb
Password: 1234

# Try reading sensitive files
$ cat /etc/shadow

# Try modifying the /etc/shadow file (Critical exploit!)
$ echo "hacked::0:0::/:/bin/bash" | tee -a /etc/shadow

# Switch back to root
su
```

---

### ✅ **Mitigation: Fixing the Security Issues**

```bash
# Restore secure permissions on /etc/shadow
sudo chmod 640 /etc/shadow
sudo chown root:shadow /etc/shadow

# Verify the fix
ls -l /etc/shadow
```

---

### ✅ **Secure Sudo Privileges**

```bash
# Edit sudoers file safely
sudo visudo
```

**Restrict Alice from switching to root** by adding:
```plaintext
rcb ALL=(ALL) !/bin/su, !/bin/bash
```
## 📌 Task 1 - Summary  

| **Step**             | **Action**                                         | **Command**                                  |
|----------------------|---------------------------------------------------|---------------------------------------------|
| 🔹 **Setup**         | Create user **rcb**                                | `sudo useradd rcb` |
|                     | Set password for **rcb**                           | `echo "rcb:password123" | sudo chpasswd` |
|                     | Verify user exists                                 | `cat /etc/passwd | grep rcb` |
|                     | Check default permissions of sensitive files       | `ls -l /etc/shadow /etc/passwd` |
|                     | Assign incorrect permissions (INSECURE)            | `sudo chmod 777 /etc/shadow` |
|                     | Verify permission changes                          | `ls -l /etc/shadow` |
| 🔹 **Exploitation**  | Switch to **rcb** (low-privileged user)           | `su - rcb` |
|                     | Try accessing sensitive files                      | `cat /etc/shadow`<br>`cat /etc/passwd` |
|                     | Try modifying `/etc/shadow` (Critical exploit!)    | `echo "hacked::0:0::/:/bin/bash" | tee -a /etc/shadow` |
|                     | Switch back to root                                | `su` |
| 🔹 **Mitigation**    | Restore secure permissions                        | `sudo chmod 640 /etc/shadow`<br>`sudo chown root:shadow /etc/shadow` |
|                     | Verify permission fix                              | `ls -l /etc/shadow` |
| 🔹 **Secure Sudo Privileges** | Edit sudoers file                   | `sudo visudo` |
|                     | Restrict **rcb** from switching to root            | Add the following in `visudo`:<br>`rcb ALL=(ALL) !/bin/su, !/bin/bash` |

---

### END -x-
