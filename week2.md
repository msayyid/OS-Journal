# **Week 2 – Security Planning and Testing Methodology**

## **Phase 2: Overview**

This week focused on designing a security baseline for both virtual machines and creating a structured performance testing methodology. The work included a formal performance testing plan, a complete security configuration checklist, and a threat model that identifies major risks with corresponding mitigation strategies.

---

# **1. Performance Testing Plan**

## Objective

To assess system performance, responsiveness, and reliability during baseline, load, and stress conditions while validating that remote monitoring via SSH functions securely and accurately.

## Environment

* Monitored VM: Ubuntu Server 22.04 LTS
* Monitoring VM: Fedora Workstation 40
* Connection Method: SSH (key-based authentication)

## Testing Tools

* stress-ng → generates CPU, memory, and I/O load
* sar (sysstat) → collects system performance data
* htop → real-time monitoring
* iperf3 → measures network throughput
* gnuplot → visualizes performance data

## Methodology

1. Establish secure SSH session
   Example: ssh -i ~/.ssh/id_rsa user@ubuntu-server

2. Record baseline performance metrics
   Example: sar -u -r 5 12

3. Apply progressive load using stress-ng
   Example: stress-ng --cpu 4 --io 2 --vm 2 --timeout 5m

4. Measure network throughput
   Example: iperf3 -c <ubuntu_ip>

5. Visualize collected data
   Example: gnuplot results.sar

## Testing Approach

* Baseline Test: Measure idle CPU, RAM, and I/O usage
* Load Test: Apply controlled stress on CPU, memory, and disk
* Stress Test: Push system beyond normal capacity to locate bottlenecks
* Monitoring Validation: Confirm that SSH monitoring reflects real-time load
* Evaluation Metrics: CPU peaks, memory averages, I/O wait, SSH latency, packet loss

---

# **2. Security Configuration Checklist**

A complete security baseline was applied to both Ubuntu and Fedora virtual machines. Each configuration step strengthens confidentiality, integrity, and system resilience.

---

## **A. SSH Hardening**

Goal: Protect against unauthorized access and brute-force attacks.

1. Edit SSH configuration:

   * sudo nano /etc/ssh/sshd_config

   Changes made:

   * PermitRootLogin no (prevents root login)
   * PasswordAuthentication no (forces key-based login)
   * Port 2222 (reduces automated scanning on port 22)

2. Apply changes:

   * sudo systemctl restart sshd

3. Install Fail2Ban:
   Ubuntu: sudo apt install fail2ban -y
   Fedora: sudo dnf install fail2ban -y
   Enable service: sudo systemctl enable --now fail2ban

Fail2Ban monitors logs and bans IPs with repeated failed login attempts.

---

## **B. Firewall Configuration**

Goal: Restrict all non-essential traffic.

### Ubuntu (UFW)

* sudo apt install ufw -y
* sudo ufw default deny incoming
* sudo ufw default allow outgoing
* sudo ufw allow 2222/tcp
* sudo ufw enable
* sudo ufw status verbose

### Fedora (Firewalld)

* sudo dnf install firewalld -y
* sudo systemctl enable --now firewalld
* sudo firewall-cmd --add-port=2222/tcp --permanent
* sudo firewall-cmd --set-default-zone=public
* sudo firewall-cmd --reload

---

## **C. Mandatory Access Control (MAC)**

Goal: Enforce least-privilege security through system policies.

### Ubuntu – AppArmor

* sudo apt install apparmor apparmor-utils -y
* sudo systemctl enable apparmor
* sudo systemctl start apparmor
* sudo aa-status (shows enforced profiles)

### Fedora – SELinux

* sestatus (shows mode)
* sudo setenforce 1 (enable enforcing mode)
* sudo nano /etc/selinux/config (make enforcing permanent)

---

## **D. Automatic Updates**

Goal: Ensure the system consistently receives security patches.

### Ubuntu

* sudo apt install unattended-upgrades -y
* sudo dpkg-reconfigure unattended-upgrades

### Fedora

* sudo dnf install dnf-automatic -y
* sudo systemctl enable --now dnf-automatic.timer

---

## **E. User Privilege Management**

Goal: Prevent privilege escalation by controlling sudo access.

* sudo adduser analyst
* sudo usermod -aG sudo analyst
* getent group sudo (verify group members)
* sudo visudo (safe way to edit sudo permissions)

Only approved users receive sudo rights, and all admin actions are logged.

---

## **F. Network Security**

Goal: Reduce attack surface and defend against spoofing or DoS attempts.

1. Disable unused interfaces:
   sudo nmcli device set eth1 managed no

2. Enable reverse-path filtering:
   echo "net.ipv4.conf.all.rp_filter=1" | sudo tee -a /etc/sysctl.conf
   sudo sysctl -p

3. Filter malformed packets + rate-limit SSH:
   sudo iptables -A INPUT -m conntrack --ctstate INVALID -j DROP
   sudo iptables -A INPUT -p tcp --dport 2222 -m limit --limit 3/minute --limit-burst 10 -j ACCEPT
   sudo iptables-save | sudo tee /etc/iptables/rules.v4

---

## **Summary of Security Baseline**

* SSH hardened (no root login, no passwords, custom port)
* Firewalls configured and active
* AppArmor / SELinux enforcing
* Automatic updates enabled
* Privilege escalation minimized
* IP spoofing protection + packet filtering enabled

---

# **3. Threat Model**

| Threat                  | Description                                                  | Mitigation                                              |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------- |
| SSH Brute-Force Attack  | Attacker repeatedly tries different passwords to gain access | Disabled passwords, Fail2Ban, port change               |
| Privilege Escalation    | Low-privilege user attempts to become root                   | Restricted sudo access, SELinux/AppArmor enforcement    |
| Denial of Service (DoS) | System overwhelmed via traffic floods or resource exhaustion | Rate-limited SSH, firewall rules, continuous monitoring |

---

**Weekly Reflection**

This week focused on planning security controls and defining how system performance would be tested later in the coursework. Creating the performance testing plan helped clarify the importance of collecting baseline measurements before applying load or stress, so results can be compared accurately. Planning remote monitoring through SSH also reinforced the need to balance system visibility with secure access.

Developing the security configuration checklist showed how multiple controls such as SSH hardening, firewalls, access control, and automatic updates work together to reduce risk. Creating the threat model helped connect common security threats to practical mitigation techniques. Overall, this week provided a clear security and testing framework that supports the implementation tasks in the following weeks.
