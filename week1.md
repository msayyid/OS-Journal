```
week1.md
```

---

# **Week 1 – System Planning and Distribution Selection**

## **Phase 1: Overview**

This week focused on planning the operating system deployment and justifying technical decisions. The tasks involved selecting suitable Linux distributions, configuring VirtualBox networking, defining the workstation–server architecture, and documenting system specifications through CLI tools.

---

# **1. System Architecture Diagram**

## **1.1 System Architecture Explanation**

The virtualized environment consists of two Linux virtual machines running inside Oracle VirtualBox on a Windows 10 host:

### **VM 1 – Ubuntu 24.04.3 LTS (Workstation)**

* Used for desktop operations, development tools, and administrative tasks
* Acts as the client system used to manage the server via SSH

### **VM 2 – Fedora 42 (Server)**

* A lightweight server environment
* Used to simulate server-side services, remote management, and OS administration tasks

### **Networking**

Both VMs use a NAT network (`10.0.2.0/24`) with:

* VirtualBox gateway: `10.0.2.2`
* DHCP-assigned guest IP: typically `10.0.2.15`
* Internet access through the host machine
* Local communication between VMs via NAT for remote management (SSH, file transfer, service testing)

This setup provides a safe, isolated environment for multi-system networking and OS configuration without affecting the host or external networks.



![Architecture Diagram](/assets/images/week_1/system-achitecture-diagram.png)

---

# **2. Distribution Selection Justification**

## **2.1 Comparison Table**

| Criterion         | **Ubuntu 24.04.3 LTS (Chosen)**                  | **Fedora 42**                     | **Debian 12**                |
| ----------------- | ------------------------------------------------ | --------------------------------- | ---------------------------- |
| Base              | Debian-based                                     | Red Hat-based                     | Debian-based                 |
| Stability         | Long-term support (5 years)                      | Frequent updates (6-month cycle)  | Very stable, slower updates  |
| Package Manager   | `apt` / `dpkg`                                   | `dnf` / `rpm`                     | `apt` / `dpkg`               |
| Ease of Use       | Very user-friendly                               | More advanced                     | Moderate                     |
| Community Support | Very large, extensive documentation              | Strong developer-focused          | Reliable but smaller         |
| Use Case Fit      | Excellent for workstation and lightweight server | Great for testing newest features | Ideal for production servers |

## **2.2 Justification**

Ubuntu 24.04.3 LTS was selected as the workstation because:

* It offers long-term support and high stability
* It integrates smoothly with VirtualBox
* It provides wide package availability and excellent documentation
* It offers a balance between modern features and reliable performance

Fedora is useful for newer technologies but updates frequently, making it less predictable for a semester-long environment.
Debian is extremely stable but slower to receive updates, which may limit access to newer tools.

---

# **3. Workstation Configuration Decision**

### **Chosen Workstation:**

**Ubuntu 24.04.3 LTS Desktop**

### **Allocated VirtualBox Resources:**

* **4 CPU cores**
* **8 GB RAM**
* **64 GB virtual disk**

### **Justification**

* Ubuntu provides a clean and beginner-friendly GNOME desktop environment
* Strong community support makes troubleshooting easier
* Lightweight enough to perform well inside VirtualBox
* Reliable for development, CLI work, and remote administration




<p align="center">
  <img src="/assets/images/week_1/ubuntu-workstation-settings.png" width="45%">
  <img src="/assets/images/week_1/fedora-server-settings.png" width="45%">
</p>
<p align="center"><em>Ubuntu workstation settings (left) and Fedora server settings (right).</em></p>



---

# **4. Network Configuration Documentation**

Both Ubuntu and Fedora VMs use **NAT networking** in VirtualBox.

This provides:

* Outbound Internet access
* Automatic IP assignment via VirtualBox DHCP
* Isolation from the external network
* Local communication between VMs via shared NAT

## **4.1 Network Settings Comparison**

| Setting      | **Ubuntu 24.04.3 (Workstation)** | **Fedora 42 (Server)**          |
| ------------ | -------------------------------- | ------------------------------- |
| Adapter Type | Intel PRO/1000 MT Desktop (NAT)  | Intel PRO/1000 MT Desktop (NAT) |
| Interface    | `enp0s3`                         | `enp0s3`                        |
| IPv4 Address | `10.0.2.15/24`                   | `10.0.2.15/24`                  |
| Gateway      | `10.0.2.2`                       | `10.0.2.2`                      |
| Broadcast    | `10.0.2.255`                     | `10.0.2.255`                    |
| Subnet Mask  | `255.255.255.0`                  | `255.255.255.0`                 |
| DNS          | Auto (VirtualBox NAT)            | Auto (VirtualBox NAT)           |



<p align="center">
  <img src="/assets/images/week_1/ubuntu-network-configuration.png" width="45%">
  <img src="/assets/images/week_1/fedora-network-configuration.png" width="45%">
</p>
<p align="center"><em>Ubuntu network configuration (left) and Fedora network configuration (right).</em></p>


---

# **5. System Specifications (CLI Documentation)**

## **5.1 Ubuntu 24.04.3 LTS (Workstation)**

### **Commands Used**

```bash
uname -a
lsb_release -a
free -h
df -h
ip addr
```



![Ubuntu uname, lsb_release -a, free -h, df-h Output](/assets/images/week_1/ubuntu-commands-1.png)



---

## **5.2 Fedora 42 (Server)**

### **Commands Used**

```bash
uname -a
cat /etc/fedora-release
free -h
df -h
ip addr
```

![Fedora uname Output](/assets/images/week_1/fedora-commands-1.png)
