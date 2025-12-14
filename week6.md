# **WEEK 6 – Performance Evaluation and Analysis**

## **Phase 6 Overview**

This phase evaluates operating system performance under different workloads using a controlled client–server setup. An Ubuntu virtual machine hosts services and is monitored for resource usage, while a Fedora virtual machine generates application load and network traffic.

Performance was measured under:

* Baseline (idle) conditions
* Application load (moderate and high)
* Post-optimisation conditions

Metrics collected include CPU usage, memory usage, disk I/O, network throughput, latency, and HTTP service response times. Results were analysed to identify bottlenecks and to quantify the impact of applied optimisations.

---

## **Test Environment**

* Host platform: VirtualBox
* Server VM: Ubuntu Linux (system under test)
* Client VM: Fedora Linux (load generation)
* Service tested: Apache HTTP Server
* Network: VM-to-VM communication via VirtualBox networking

### **System Information – Ubuntu Server**

Commands used: `hostname`, `uname -a`, `lscpu`, `free -h`, `lsblk`

<p align="center">
  <img src="/assets/images/week_6/sc01_ubuntu_system_info.png" width="70%">
</p>

<p align="center">
  <img src="/assets/images/week_6/sc01_1_ubuntu_system_info_extended.png" width="70%">
</p>

---

### **System Information – Fedora Client**

Commands used: `hostname`, `uname -a`, `free -h`

<p align="center">
  <img src="/assets/images/week_6/sc02_fedora_system_info.png" width="70%">
</p>

---

## **Baseline Performance Testing (Idle System)**

### **Baseline CPU Usage**

Baseline CPU utilisation was measured on the Ubuntu server while idle using `sar -u`. The results show a very high idle percentage, confirming that the system was not under load and providing a clean reference point for later comparisons.

<p align="center">
  <img src="/assets/images/week_6/sc03_baseline_cpu_sar.png" width="70%">
</p>

---

### **Baseline Memory Usage**

Baseline memory usage was measured using `sar -r`. The output shows stable memory consumption with a large amount of free and available memory, indicating no memory pressure prior to testing.

<p align="center">
  <img src="/assets/images/week_6/sc04_baseline_memory_sar.png" width="70%">
</p>

---

### **Baseline Disk I/O Performance**

Disk I/O performance was measured using `iostat -xz` while the system was idle. Results show near-zero disk utilisation, low wait times, and minimal read/write activity.

<p align="center">
  <img src="/assets/images/week_6/sc05_baseline_disk_iostat.png" width="70%">
</p>

<p align="center">
  <img src="/assets/images/week_6/sc05_1_baseline_disk_iostat_extended.png" width="70%">
</p>

---

## **Baseline Latency and Service Response Times**

### **Apache Service Verification**

The Apache HTTP service was installed, started, and verified to be running before latency and response-time testing.

<p align="center">
  <img src="/assets/images/week_6/sc06_apache_status.png" width="70%">
</p>

---

### **Baseline Local HTTP Response Time**

Local HTTP response times were measured on the Ubuntu server using `curl`. Testing locally removes network latency and isolates Apache service processing performance.

Results show consistently low response times, indicating efficient service behaviour under no load.

<p align="center">
  <img src="/assets/images/week_6/sc07_local_http_response.png" width="70%">
</p>

---

### **Baseline Network Latency**

Network latency was measured using ICMP ping from the Fedora client to the Ubuntu server under idle conditions. The results show consistently low round-trip times, providing a reference for later comparison.

<p align="center">
  <img src="/assets/images/week_6/sc08_network_latency_ping.png" width="70%">
</p>

---

### **Baseline Remote HTTP Response Time**

Remote HTTP response times were measured from Fedora to Ubuntu using `curl`. These response times are noticeably higher than local measurements, demonstrating the impact of network and virtualisation overhead.

<p align="center">
  <img src="/assets/images/week_6/sc09_remote_http_response.png" width="70%">
</p>

---

## **Baseline Network Throughput**

Baseline network throughput was measured using `iperf3`, with Ubuntu acting as the server and Fedora as the client.

<p align="center">
  <img src="/assets/images/week_6/sc10_iperf3_baseline.png" width="70%">
</p>

**Results Summary:**

* Test duration: ~20 seconds
* Average throughput: ~460 Mbit/s
* Data transferred: ~1.08 GB
* Packet retransmissions: minimal

These results indicate a stable, high-bandwidth virtual network environment.

---

## **Application Load Testing (Pre-Optimisation)**

### **Moderate Load**

A moderate HTTP workload was generated locally using ApacheBench. This test evaluates Apache performance without network-induced latency.

<p align="center">
  <img src="/assets/images/week_6/sc11_ab_moderate_pre.png" width="70%">
</p>

---

### **High Load Testing – Application Load and System Monitoring (Pre-Optimisation)**

A high-intensity HTTP workload was generated locally on the Ubuntu server using ApacheBench to evaluate system behaviour under peak application load. At the same time, system activity was monitored to observe CPU scheduling, process activity, and overall responsiveness.

**Command used (ApacheBench – high load):**

```
ab -n 2000 -c 50 http://127.0.0.1/
```

<p align="center">
  <img src="/assets/images/week_6/sc12_ab_high_pre.png" width="70%">
</p>

The ApacheBench output shows increased request throughput and concurrency, confirming that the Apache service was actively handling a high number of simultaneous requests prior to optimisation.

**Command used (system monitoring during load):**

```
top -b -d 1
```

<p align="center">
  <img src="/assets/images/week_6/sc13_top_during_load_pre.png" width="70%">
</p>

The `top` output confirms multiple active Apache worker processes, increased CPU utilisation compared to baseline, and stable memory usage. Despite the high workload, the system remained responsive, indicating no immediate CPU or memory saturation at this stage.

---

## **Performance Analysis and Bottleneck Identification**

Baseline testing showed:

* Very low CPU utilisation
* Minimal memory usage
* Negligible disk I/O
* Stable network throughput

Under application load, Apache handled concurrent requests efficiently. CPU usage increased moderately but remained well below saturation. Disk activity remained minimal, and memory usage stayed stable.

Remote HTTP testing showed significantly higher response times than local testing, indicating that **network and virtualisation overhead**, rather than Apache processing or system resources, was the primary bottleneck.

---

## **Optimisation Techniques**

### **Optimisation 1 – HTTP Compression**

Apache HTTP compression was enabled using the `mod_deflate` module. This reduces the size of HTTP responses, lowering network overhead and improving response efficiency.

<p align="center">
  <img src="/assets/images/week_6/sc14_mod_deflate_enabled.png" width="70%">
</p>

---

### **Optimisation 2 – KeepAlive Configuration**

Persistent connections (KeepAlive) were enabled and tuned to reduce TCP connection overhead under concurrent access.

<p align="center">
  <img src="/assets/images/week_6/sc15_apache_keepalive_status.png" width="70%">
</p>

---

### **Post-Optimisation Moderate Load Testing**

After enabling HTTP compression and tuning Apache connection handling, the moderate workload test was repeated to evaluate the impact of the applied optimisations on application performance.

**Command used (ApacheBench – moderate load):**

```
ab -n 1000 -c 20 http://127.0.0.1/
```

<p align="center">
  <img src="/assets/images/week_6/sc16_ab_moderate_post.png" width="70%">
</p>

The ApacheBench output shows an increase in requests per second and a reduction in average response time compared to pre-optimisation results, indicating improved efficiency in request handling.

<p align="center">
  <img src="/assets/images/week_6/sc16_1_ab_moderate_post.png" width="70%">
</p>

The extended output confirms that all requests were completed successfully with no failed requests and stable response times, demonstrating that the optimised configuration performs reliably under moderate concurrent load.


---

### **High Load**

High-load application testing was repeated after optimisation to assess Apache behaviour under peak concurrency. System monitoring was enabled to observe resource usage during the test.

**ApacheBench results (high load, post-optimisation):**

<p align="center">
  <img src="/assets/images/week_6/sc17_ab_high_post.png" width="70%">
</p>

<p align="center">
  <img src="/assets/images/week_6/sc17_1_ab_high_post.png" width="70%">
</p>

**System monitoring during high load (post-optimisation):**

<p align="center">
  <img src="/assets/images/week_6/sc18_top_during_load_post.png" width="70%">
</p>


---

## **Task 10 – Optimisation Testing and Analysis**

### **Objective**

The objective of this task was to evaluate the impact of Apache optimisations under increased workload by comparing pre-optimisation and post-optimisation performance.

### **Testing Methodology**

Application-level performance testing was conducted using ApacheBench under:

* Moderate load: 1000 requests, concurrency 20
* High load: 2000 requests, concurrency 50

Tests were executed locally to eliminate network variability and focus on Apache and operating system behaviour.

### **Results and Analysis**

* Requests per second increased significantly after optimisation
* Mean time per request decreased under both moderate and high load
* No failed requests were observed post-optimisation
* CPU utilisation remained low with high idle percentages
* Memory usage remained stable with no swapping
* Disk I/O activity remained minimal

These results confirm that HTTP compression and improved connection handling increased efficiency without introducing new bottlenecks.

---

## **Performance Visualisations and Analysis**

### **Apache Throughput Comparison**

<p align="center">
  <img src="/assets/images/week_6/graph01_apache_throughput.png" width="70%">
</p>

Throughput increased from approximately **3,247 requests/sec** pre-optimisation to **9,088 requests/sec** post-optimisation under moderate load. This demonstrates a substantial improvement in request-handling capacity.

---

### **HTTP Response Time Under Load**

<p align="center">
  <img src="/assets/images/week_6/graph02_http_response_time.png" width="70%">
</p>

Mean response times decreased:

* Moderate load: ~6.1 ms → ~2.2 ms
* High load: ~9.0 ms → ~5.6 ms

This shows that the optimised configuration maintains lower latency even under higher concurrency.

---

### **Network Latency and Throughput**

<p align="center">
  <img src="/assets/images/week_6/graph03a_network_latency.png" width="70%">
</p>

<p align="center">
  <img src="/assets/images/week_6/graph03b_network_throughput.png" width="70%">
</p>

Average latency remained low (~0.6 ms), and throughput remained high (~460 Mbit/s), confirming that network bandwidth was sufficient and not a limiting factor.

---

## **Conclusion**

This phase demonstrated how systematic performance testing can identify bottlenecks and guide effective optimisation. The applied Apache optimisations significantly improved throughput and reduced response times while maintaining stable CPU, memory, and disk usage. Quantitative results and visual evidence confirm improved scalability and efficiency under increased workloads.

---

## Reflection

This week provided practical experience in evaluating operating system performance under controlled workloads and interpreting quantitative performance data. By establishing clear baseline measurements and then introducing application-level load, it became easier to understand how system resources behave under different conditions and how performance bottlenecks can be identified through structured testing.

A key learning outcome was the importance of separating service-level performance from network and virtualisation effects. Local Apache testing showed consistently low response times, while remote testing introduced additional latency despite high available network throughput. This highlighted that latency, rather than bandwidth or CPU capacity, was the primary limiting factor during remote access in a virtualised environment.

Implementing and testing performance optimisations reinforced the value of data-driven tuning. Enabling HTTP compression and configuring persistent connections resulted in measurable improvements in throughput and response time without increasing resource pressure. Comparing pre- and post-optimisation results demonstrated how relatively small configuration changes can significantly improve scalability and efficiency when validated through systematic measurement.

Overall, this phase strengthened my ability to design performance experiments, analyse operating system behaviour under load, and justify optimisation decisions using quantitative evidence.

