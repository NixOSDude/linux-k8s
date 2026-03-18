# Keystone Project: Hardened Private Cloud Infrastructure

## Project Overview
This repository documents the implementation of a production-grade infrastructure foundation hosted on high-performance bare-metal hardware. I engineered this environment to demonstrate advanced Linux system administration, proactive security hardening, and modern cryptographic standards.

## Phase I: Infrastructure & Build Environment

### High-Performance Control Station (The Engine)
* **Hardware:** ASRock B860 Pro-A
* **Processor:** Intel Core Ultra 7 265KF (20 Cores / 20 Threads)
* **Memory:** 64.0 GiB DDR5
* **Host OS:** Ubuntu 24.04.4 LTS
* **Virtualization:** KVM/libvirt (CentOS 9 Stream Guest)
* **Resource Optimization:** I allocated **12 vCPUs** to the primary node to facilitate high-concurrency cryptographic builds and system stress testing.

### Host-Side Storage & Volume Mapping
I verified the physical storage paths on the **Ultra 7** host to ensure optimal I/O throughput:
* **Storage Pool:** `images` (Mapped to the NVMe-backed LVM root partition)
* **Active Disk:** `centos9-stream.qcow2`
* **Provisioning Source:** `CentOS-Stream-9-latest-x86_64-dvd1.iso`

---

## Phase II: Security Hardening & Post-Quantum Fusion

### Post-Quantum Cryptography (PQC) Integration
To secure the infrastructure against future quantum threats, I integrated the **Open Quantum Safe (OQS)** library into the environment.

* **Implementation:** I compiled **liboqs 0.15.0** from source, utilizing the **12-thread parallel processing** capabilities of the Ultra 7.
* **Algorithm Verification:** I successfully verified the **ML-KEM-768** (NIST-standardized Kyber) handshake, confirming the system's ability to perform quantum-resistant key encapsulation.
* **System Integration:** Full system-wide library availability in `/usr/local/lib64` via `ldconfig`.

### SELinux Mandatory Access Control (MAC)
I implemented hardening for custom service directories by configuring SELinux type enforcement. I transitioned custom web roots to the `httpd_sys_content_t` context and ensured policy persistence using `semanage` and `restorecon` to mitigate unauthorized lateral movement.

---

## Current Infrastructure Status

| Component | Role | OS | Status | Verified Tech |
| :--- | :--- | :--- | :--- | :--- |
| **Infrastructure Node 1** | Primary Host | CentOS 9 | **Hardened** | ML-KEM-768 / SELinux |
| **Ultra7 Host** | Bare-Metal Hypervisor | Ubuntu 24.04 | **Active** | KVM / libvirt |
