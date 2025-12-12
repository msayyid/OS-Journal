# **WEEK 5 – Advanced Security and Monitoring Infrastructure**

## **Phase 5 Overview**

This week focused on implementing advanced security controls on the Ubuntu server and developing monitoring capabilities from the Fedora workstation. All tasks were performed strictly via SSH, as required by the module brief.

The following deliverables were completed:

1. AppArmor access control implementation and verification
2. Automatic security updates configuration
3. Fail2ban intrusion detection setup
4. Creation of a complete security baseline verification script (security-baseline.sh)
5. Creation of a remote monitoring script (monitor-server.sh) to collect metrics via SSH

Screenshot placeholders are included where applicable.

---

# **5.1 AppArmor Access Control**

Ubuntu uses AppArmor as its Mandatory Access Control (MAC) system. The goal this week was to demonstrate that AppArmor is active, enforce an existing profile, and show how access control events can be viewed.

### Steps Completed:

**Installed AppArmor tools**
Packages installed to manage and inspect AppArmor profiles.

**Screenshot Placeholder:**
(Insert screenshot of AppArmor installation completed in terminal)

<p align="center">
  <img src="/assets/images/week_5/apparmor-installed.png" width="70%">
</p>

---

**Checked AppArmor status using aa-status**
This displays all profiles, including those in enforce and complain mode.

**Screenshot Placeholder:**
(Insert screenshot of aa-status output showing loaded/enforced profiles)

<p align="center">
  <img src="/assets/images/week_5/aa-status-output.png" width="70%">
</p>

---

**Enforced an existing AppArmor profile**
Since modern Ubuntu versions no longer include an sshd profile, an existing profile such as /usr/bin/man was enforced.

**Screenshot Placeholder:**
(Insert screenshot showing aa-enforce applied and profile in enforce mode)

<p align="center">
  <img src="/assets/images/week_5/aa-enforce-man.png" width="70%">
</p>

---

**Viewed AppArmor log events**
System logs were filtered to show AppArmor-related entries.

**Screenshot Placeholder:**
(Insert screenshot of journalctl output filtered by AppArmor)

<p align="center">
  <img src="/assets/images/week_5/apparmor-logs.png" width="70%">
</p>

---

# **5.2 Automatic Security Updates**

Automatic security updates were configured using unattended-upgrades.

### Steps Completed:

**Installed unattended-upgrades**
Enables automatic installation of important security patches.

**Enabled automatic updates** using dpkg-reconfigure
This activates periodic unattended upgrades.

**Verified configuration** inside `/etc/apt/apt.conf.d/20auto-upgrades`.

**Screenshot Placeholder:**
(Insert screenshot of 20auto-upgrades file showing enabled settings)

<p align="center">
  <img src="/assets/images/week_5/auto-upgrades-config.png" width="70%">
</p>

---

**Checked service status and logs**

**Screenshot Placeholder:**
(Insert screenshot of systemctl status unattended-upgrades)

<p align="center">
  <img src="/assets/images/week_5/unattended-upgrades-status.png" width="70%">
</p>

**Screenshot Placeholder:**
(Insert screenshot of unattended-upgrades.log output)

<p align="center">
  <img src="/assets/images/week_5/unattended-upgrades-log.png" width="70%">
</p>

---

# **5.3 Fail2ban Intrusion Detection**

Fail2ban was installed and configured to protect SSH from brute-force attempts.

### Steps Completed:

**Installed fail2ban**
Available on Ubuntu via apt.

**Copied configuration file**
A local copy (jail.local) was created for safe editing.

**Configured SSH protection**
Enabled SSH jail with settings:

* enabled = true
* maxretry = 5
* bantime = 600 seconds

**Restarted and enabled fail2ban service**
Ensures protection is active across reboots.

**Verified fail2ban operation**

**Screenshot Placeholder:**
(Insert screenshot of fail2ban-client status output)

<p align="center">
  <img src="/assets/images/week_5/fail2ban-status.png" width="70%">
</p>

**Screenshot Placeholder:**
(Insert screenshot of fail2ban-client status sshd output)

<p align="center">
  <img src="/assets/images/week_5/fail2ban-ssh-status.png" width="70%">
</p>

---

# **5.4 Security Baseline Verification Script (security-baseline.sh)**

A script named security-baseline.sh was created and stored on the Ubuntu server.
It verifies all security configurations from Phases 4 and 5, including:

* SSH hardening settings
* Firewall rules
* Automatic update configuration
* Fail2ban status
* AppArmor status

The script contains line-by-line comments explaining each check.

It was made executable and run remotely from Fedora using:

ssh user@server "bash ~/security-baseline.sh"

**Screenshot Placeholder:**
(Insert screenshot showing security-baseline.sh executing over SSH)

<p align="center">
  <img src="/assets/images/week_5/security-baseline-script-run.png" width="70%">
</p>

---

# **5.5 Remote Monitoring Script (monitor-server.sh)**

A remote monitoring script named monitor-server.sh was created on Fedora.
This script connects to the Ubuntu server via SSH and retrieves:

* Hostname
* Kernel version
* Uptime
* CPU usage
* Memory usage
* Disk usage
* Logged-in users

The script is executed as:

./monitor-server.sh user@server_ip

**Screenshot Placeholder:**
(Insert screenshot of monitor-server.sh output in Fedora terminal)

<p align="center">
  <img src="/assets/images/week_5/monitor-server-output.png" width="70%">
</p>

---
