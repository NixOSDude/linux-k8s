# Keystone K8s Project: Production-Grade Private Cloud

## Project Overview
This repository documents the implementation of a production-grade private cloud environment hosted on high-performance bare-metal hardware. I designed this infrastructure to demonstrate advanced Linux system administration, security hardening, and modern cryptographic standards.

The architecture transitions from raw virtualized resources to a hardened, quantum-resistant Kubernetes environment.

## Phase I: Infrastructure & Build Environment

### High-Performance Control Station (The Engine)
* **Hardware:** ASRock B860 Pro-A
* **Processor:** Intel Core Ultra 7 265KF (20 Cores / 20 Threads)
* **Memory:** 64.0 GiB DDR5
* **Host OS:** Ubuntu 24.04.4 LTS
* **Virtualization:** KVM/libvirt
* **Guest OS:** CentOS 9 Stream
* **Resource Optimization:** I allocated **12 vCPUs** to the primary lab node to facilitate high-concurrency cryptographic builds.

### Host-Side Storage & Volume Mapping
I verified the physical storage paths on the **Ultra 7** host to ensure optimal I/O throughput:
* **Storage Pool:** `images` (Mapped to the NVMe-backed LVM root partition)
* **Physical Path:** `/var/lib/libvirt/images`
* **Active Disk:** `centos9-stream.qcow2`
* **Provisioning Source:** `CentOS-Stream-9-latest-x86_64-dvd1.iso`

---

## Phase II: Security Hardening & Post-Quantum Fusion

### Post-Quantum Cryptography (PQC) Integration
To secure the cluster against future quantum threats, I integrated the **Open Quantum Safe (OQS)** library into the environment.

* **Implementation:** I compiled **liboqs 0.15.0** from source, utilizing the **12-thread parallel processing** capabilities of the Ultra 7 via the Ninja build system.
* **Algorithm Verification:** I successfully verified the **ML-KEM-768** (NIST-standardized Kyber) handshake, confirming the system's ability to perform quantum-resistant key encapsulation.
* **System Integration:** Full system-wide library availability in `/usr/local/lib64` via `ldconfig`.

### SELinux Mandatory Access Control (MAC)
I implemented hardening for custom service directories by configuring SELinux type enforcement. I transitioned custom web roots to the `httpd_sys_content_t` context and ensured policy persistence using `semanage` and `restorecon` to mitigate unauthorized lateral movement.

---

## Phase III: Network & Storage (In Progress)

### Network Architecture
I implemented a static IP schema to ensure predictable node communication and cluster stability. 
* **Control Plane IP:** 192.168.122.240
* **Status:** Connectivity verified via SSH and PQC Handshake.

### Storage Strategy
* **LVM Orchestration:** Planned implementation of Logical Volume Management (LVM) to provide a flexible storage backend for Kubernetes PersistentVolumes (PVs).

---

## Current Infrastructure Status

| Component | Role | OS | Status | Verified Tech |
| :--- | :--- | :--- | :--- | :--- |
| **k8s-control** | Control Plane | CentOS 9 | **Hardened** | ML-KEM-768 / SELinux |
| **Ultra7 Host** | Hypervisor | Ubuntu 24.04 | **Active** | KVM / libvirt |

---

### Next Step
I am currently transitioning to **Lab 20 (Manage Partitions and Swap Space)** to build out the LVM storage layer and prepare for the **CKA Orchestration Layer**.
