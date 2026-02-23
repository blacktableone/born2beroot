*This project has been created as part of the 42 curriculum by nisu.*

# Born2beroot

## Description
Born2beroot is a system administration project that introduces the fundamentals of virtual machines, operating systems, and server security. The goal of this project is to build a secure, headless server from scratch. It involves setting up an encrypted file system using LVM, enforcing strict user and sudo policies, configuring a firewall and SSH access, and writing a bash script to monitor system metrics in real-time.

## Instructions
Since this project consists of a Virtual Machine, you will need a hypervisor to run it.
1. Ensure **VirtualBox** is installed on your host machine.
2. Open the `.vdi` (Virtual Disk Image) provided in this setup within VirtualBox.
3. Start the Virtual Machine.
4. Unlock the encrypted disk using the LUKS passphrase.
5. You can log in directly via the terminal or connect remotely using SSH:
   `ssh [your_login]@localhost -p 4242` (or use the VM's specific IP address).

---

## Project Description & Design Choices

### Operating System Choice: Debian
For this project, I chose **Debian** over Rocky Linux. 
* **Pros:** Debian is highly renowned for its stability, extensive community documentation, and user-friendly package manager (`apt`). It also uses AppArmor by default, which is generally more approachable for beginners than SELinux.
* **Cons:** Because it prioritizes stability, its software packages can be older compared to cutting-edge distributions.

### Main Design Choices
* **Partitioning:** The disk is fully encrypted using LUKS. I implemented Logical Volume Management (LVM) to separate critical directories from the root partition. This prevents user data or runaway log files from crashing the entire system by consuming all root disk space.
* **Security Policies:** A strict password policy is enforced using `libpam-pwquality` (minimum 10 characters, complexity requirements, and a 30-day expiration cycle). Sudo access is heavily restricted (max 3 attempts, isolated log files, secure path enforcement).
* **User Management:** Remote SSH login for the `root` user is entirely disabled. Administration must be done by logging in as a standard user belonging to the `user42` and `sudo` groups, and escalating privileges when necessary.
* **Services Installed:** A minimal setup was chosen. Only `openssh-server` (configured on port 4242) and `ufw` (Uncomplicated Firewall, restricting all incoming traffic except port 4242) are running alongside base system utilities.

### Technical Comparisons

| Feature | Option 1 | Option 2 | Comparison |
| :--- | :--- | :--- | :--- |
| **OS** | **Debian** | **Rocky Linux** | Debian uses `.deb` packages and the `apt` manager. It is community-driven and highly stable. Rocky Linux is a downstream, complete binary-compatible release using the Red Hat Enterprise Linux (RHEL) source code, utilizing `.rpm` and `dnf`. |
| **Security** | **AppArmor** | **SELinux** | AppArmor (used in Debian) restricts programs by associating security profiles with specific file paths. SELinux (used in Rocky) is more complex and granular, assigning security contexts to all files, processes, and ports. |
| **Firewall** | **UFW** | **firewalld** | UFW (Uncomplicated Firewall) is a user-friendly frontend for iptables, designed for simple port/IP blocking. Firewalld uses "zones" to manage trust levels for different network connections, making it better for complex routing. |
| **Hypervisor** | **VirtualBox** | **UTM** | VirtualBox is a cross-platform type-2 hypervisor by Oracle, primarily using x86/x64 architecture virtualization. UTM is designed specifically for macOS (especially Apple Silicon/M-chips) and uses QEMU under the hood to emulate different architectures (like x86 on ARM). |

```
Defaults    passwd_tries=3
Defaults    badpass_message="Wrong password, contact nisu for help!"
Defaults    logfile="/var/log/sudo/sudo.log"
Defaults    log_input, log_output
Defaults    iolog_dir="/var/log/sudo"
Defaults    requiretty
Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

---

## command use

### Users & groups
```shell
 getent group sudo
 getent group user42
 sudo adduser <new_user>
 sudo useradd <new_user>
 sudo passwd <new_user>
 sudo getent shadow <new_user> # check if user assed successful
 sudo groupadd <groupname>
 sudo usermod -aG <groupname> <username>
 groups <username>
 su - <username> # change to newuser
 exit # back older user
 sudo deluser <username>
 sudo deluser --remove-home <username> # full delete
 sudo userdel -r -f <username> # force delete
 sudo delgroup <group_name>
```

### Password policy
```shell
sudo chage -l <username>
/etc/login.defs
/etc/pam.d/common-password # using libpam-pwquality
sudo passwd root # change root passwd
passwd  # change personal passwd
```

### Hostname and partitions
```shell
hostnamectl
sudo hostnamectl set-hostname <new_hostname>
sudo reboot
lsblk
/etc/hostname

```

### Sudo
```shell
sudo visudo
cd /var/log/sudo # permission denied?
sudo ls /var/log/sudo
sudo cat /var/log/sudo/sudo.log
ls
cat sudo.log
```
### UFW
```shell
sudo ufw status numbered
sudo ufw allow <new_number>
sudo ufw delete <number> # how to check number
```
### SSH
```shell
sudo service ssh status
ssh <new_user>@local.host -p 4242
```
### Script Monitoring
```shell
/usr/local/bin
sudo sh /usr/local/bin/monitoring.sh # sh means shell
sudo crontab -u root -e
sudo systemctl stop cron
sudo systemctl start cron
```
### Signature
```shell
cd /sgoinfre/students/<your_intra_username>/<your_VM_name>
shasum <your_VM_name>.vdi
signature.txt
```

### Resources
- [Born2BeRoot guide sourced by gemartin99)](https://42-cursus.gitbook.io/guide](https://noreply.gitbook.io/born2beroot))
- [A general gitbook by Laura & Simon, from Switzerland (42 Lausanne)](https://42-cursus.gitbook.io/guide)

### AI Usage
Claude (claude.ai) was used to:
- Generate this `README.md` structure based on the project's README requirements.
- Better understand the concept

---
