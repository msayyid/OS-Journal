# **WEEK 3 – Application Selection for Performance Testing**

## **Phase 3 Overview**

This week focused on selecting applications that represent different workload types for performance testing, documenting installation steps for each tool, estimating their expected resource usage, and creating a monitoring strategy to capture performance metrics during testing.

The goal was to build a structured and realistic workload environment covering CPU, RAM, disk I/O, network, and server-level activity.

---

# **1. Application Selection Matrix**

Below is the matrix of selected applications, categorized by workload type and justified based on relevance and characteristics.

**CPU-Intensive – Stress-ng**
Chosen because it is an industry-standard tool that can generate deterministic, high CPU load with configurable worker threads. Useful for stressing multi-core environments.

**RAM-Intensive – Stress-ng (vm stressor)**
The vm stressor allocates memory, performs memcpy operations, and creates high memory pressure. Ideal for testing memory bandwidth and swap behavior.

**I/O-Intensive – Fio**
A widely used tool for evaluating disk performance. Highly configurable, supports sequential and random read/write patterns, adjustable block sizes, and JSON output.

**Network-Intensive – iPerf3**
A standard tool for network throughput, latency, and jitter testing. Supports both TCP and UDP, and operates in a client-server model.

**Server Application – Minecraft Server (Java Edition)**
A lightweight real-world server workload that uses CPU, RAM, disk I/O, and network resources. Provides logs and predictable behavior, making it useful for multi-resource performance evaluation.

---

# **2. Installation Documentation (SSH Commands)**

The following applications were installed over SSH on the Ubuntu Server VM.

**Stress-ng (CPU and RAM workloads)**

* Update repositories
* Install using: sudo apt install -y stress-ng

**Fio (Disk I/O workloads)**

* Update repositories
* Install using: sudo apt install -y fio

**iPerf3 (Network performance)**
Server side:

* Install using: sudo apt install -y iperf3
* Start server: iperf3 -s

Client side:

* Install using: sudo apt install -y iperf3
* Connect to server: iperf3 -c <SERVER_IP>

**Minecraft Server (Java Edition)**
Requirements: Java 17
Steps:

* Install openjdk-17-jre-headless
* Create a folder for the server
* Download the server JAR
* Accept EULA by creating eula.txt
* Run server with correct RAM allocation

---

# **3. Expected Resource Profiles**

Below is an overview of the anticipated resource usage patterns for each application:

**Stress-ng (CPU mode)**

* CPU Usage: Extremely high (close to 100% per core)
* Memory Usage: Low
* Disk I/O: Minimal
* Network: None
* Notes: Scales with number of workers

**Stress-ng (RAM mode)**

* CPU Usage: Moderate
* Memory Usage: Extremely high depending on allocation
* Disk I/O: Moderate if swapping occurs
* Network: None

**Fio (Disk I/O)**

* CPU Usage: Low–medium
* Memory Usage: Medium
* Disk I/O: Very high
* Network: None
* Notes: Behavior depends on read/write patterns

**iPerf3 (Network testing)**

* CPU Usage: Low
* Memory Usage: Low
* Disk I/O: None
* Network: Very high throughput
* Notes: Measures raw bandwidth

**Minecraft Server**

* CPU Usage: Medium–high
* Memory Usage: Medium–high (2–3 GB normal)
* Disk I/O: Medium during chunk generation
* Network: Medium
* Notes: Represents a realistic multi-resource server workload

---

# **4. Monitoring Strategy**

A combination of live and logged monitoring tools will be used to capture performance metrics during each workload type.

## **4.1 Tools Used**

* **htop** – Real-time CPU, memory, and process monitoring
* **iostat** – Disk throughput and utilization
* **vmstat** – Memory pressure, process queue, swap activity
* **iftop / nload** – Network throughput visualization
* **dstat** – Combined CPU, RAM, I/O, and network metrics
* **journalctl** – Application logs (especially for Minecraft)

---

## **4.2 Measurement Approach (Per Workload)**

**CPU-Intensive (Stress-ng CPU)**

* Use htop to view per-core saturation
* Use mpstat to observe CPU utilization over time
* Optional: check for thermal throttling

**RAM-Intensive (Stress-ng vm)**

* Monitor swap usage using vmstat
* Compare free -h output before/during/after test
* Capture OOM (Out-Of-Memory) events via dmesg

**Disk I/O-Intensive (Fio)**

* Use iostat -x to record disk read/write throughput
* Monitor utilization (%util)
* Export fio JSON logs for post-analysis

**Network-Intensive (iPerf3)**

* Use iftop or nload to watch real-time bandwidth
* Save iPerf3 test logs from both server and client

**Minecraft Server (Game Server)**

* Use top/htop to track CPU and memory
* Use iotop to measure chunk-loading disk I/O
* Use iftop to monitor player-related traffic
* Check server tick rate (TPS) through server logs
