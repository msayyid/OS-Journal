# **WEEK 3 – Application Selection for Performance Testing**

## **Phase 3 Overview**

This week focused on selecting applications that represent different workload types for performance testing, documenting installation steps for each tool, estimating their expected resource usage, and creating a monitoring strategy to capture performance metrics during testing.

The goal was to build a structured and realistic workload environment covering CPU, RAM, disk I/O, network, and server-level activity.

---

# **1. Application Selection Matrix**

Below is the matrix of selected applications, categorized by workload type and justified based on relevance and characteristics.

| Workload Type      | Application                     | Justification                                                                      | Key Characteristics                                                      |
| ------------------ | ------------------------------- | ---------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| CPU-Intensive      | Stress-ng                       | Industry-standard tool allowing fine-grained CPU stress (threads, ops/sec).        | High CPU load, multi-threading, deterministic workload.                  |
| RAM-Intensive      | Stress-ng (vm stressor)         | Enables stressing memory through allocation, memcpy, and random writes.            | High memory pressure, swap testing, memory bandwidth evaluation.         |
| I/O-Intensive      | Fio                             | Most widely used tool for disk evaluation; supports many configurable workloads.   | Sequential/random reads/writes, block size tuning, detailed I/O metrics. |
| Network-Intensive  | iPerf3                          | Standard tool for measuring throughput, latency, and jitter in client-server mode. | Measures TCP/UDP bandwidth, low overhead, controlled testing.            |
| Server Application | Minecraft Server (Java Edition) | Lightweight but realistic server load involving CPU, RAM, I/O, and network usage.  | Real-world multi-resource workload; predictable behaviour; useful logs.  |

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

| Application           | CPU Usage                  | Memory Usage                    | Disk I/O            | Network Usage | Notes                                                               |
| --------------------- | -------------------------- | ------------------------------- | ------------------- | ------------- | ------------------------------------------------------------------- |
| Stress-ng (CPU mode)  | Very high (near 100%/core) | Low                             | Minimal             | None          | Fully controlled via number of workers.                             |
| Stress-ng (RAM mode)  | Moderate                   | Very high (GB-level allocation) | Moderate (swap I/O) | None          | Tests memory pressure, bandwidth, and swap performance.             |
| Fio (Disk I/O)        | Low–medium                 | Medium                          | Very high           | None          | Patterns depend on sequential or random R/W workloads.              |
| iPerf3 (Network test) | Low                        | Low                             | None                | Very high     | Measures raw maximum network throughput.                            |
| Minecraft Server      | Medium–high                | Medium–high (2–3 GB typical)    | Medium              | Medium        | Real-world combined workload: chunk loading, ticks, player traffic. |



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

---

**Reflection**

This week focused on selecting suitable applications to represent different system workloads and understanding how each one stresses specific system resources. Comparing CPU, memory, disk, network, and server-level workloads highlighted that performance testing requires more than a single tool or metric. Each application was chosen to isolate or combine resource usage in a controlled and meaningful way.

Defining expected resource profiles helped clarify what behaviour should be considered normal versus abnormal during testing. Planning the monitoring strategy in advance also reinforced the importance of selecting the right tools for each workload. Overall, this week provided a structured foundation for conducting accurate and reproducible performance tests in the following phase.
