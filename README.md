# Keystone K8s Project: Production-Grade Private Cloud

## Project Overview
This repository documents the implementation of a production-grade private cloud environment. I engineered this infrastructure from the ground up to demonstrate advanced Linux system administration, storage orchestration, and virtualized networking. 

My approach utilizes a "no-bloat" infrastructure-as-code methodology, transitioning from raw hardware to a hardened, quantum-resistant Kubernetes environment.

## Phase I: Hybrid Infrastructure Layer

### 1. Bare-Metal Provisioning Host
* **Model:** Dell Latitude E5570
* **Role:** Dedicated Bare-Metal Hypervisor
* **Processor:** Intel Core i7-6820HQ (4 Cores / 8 Threads)
* **Memory:** 24GiB DDR4
* **Hypervisor:** KVM/libvirt on Ubuntu 24.04 LTS

### 2. High-Performance Build & Control Station
* **Hardware:** ASRock B860 Pro-A | Intel Core Ultra 7 265KF (20 Cores / 20 Threads)
* **Role:** Parallelized build engine and secure SSH gateway.
* **Optimization:** I leveraged this 20-thread architecture to execute high-intensity cryptographic compilations for the cluster nodes.

---

## Phase II: Storage & Network Orchestration

### Storage Virtualization (LVM)
I utilized **Logical Volume Management (LVM)** to provide a flexible and high-performance storage backend. This allows for dynamic resizing of node storage without downtime.

1. **Initialize Physical Volume:**
   \`\`\`bash
   sudo pvcreate /dev/sda3
   \`\`\`
2. **Create Infrastructure Volume Group:**
   \`\`\`bash
   sudo vgcreate vg_infrastructure /dev/sda3
   \`\`\`
3. **Provision Logical Volumes:**
   I allocated 50GiB per node to ensure sufficient overhead for container images and persistent volumes.

---

## Phase III: Security Hardening & Post-Quantum Integration

### Post-Quantum Cryptography (PQC) Fusion
To secure the cluster against future quantum threats, I integrated the **Open Quantum Safe (OQS)** library into the CentOS 9 Stream environment.

* **Implementation:** I compiled **liboqs 0.15.0** from source, utilizing **12 virtualized threads** of the Ultra 7 processor to optimize the build.
* **Algorithm Verification:** I successfully verified the **ML-KEM-768** (NIST-standardized Kyber) handshake, ensuring the infrastructure is resistant to Shor's algorithm-based attacks.
* **System Integration:** Full system-wide library availability in `/usr/local/lib64` via `ldconfig`.

### SELinux Mandatory Access Control (MAC)
I implemented strict SELinux policies to confine web services and sensitive directories. I transitioned custom web roots to the `httpd_sys_content_t` type and ensured policy persistence using `semanage` and `restorecon`.

---

## Current Infrastructure Status

| Node Name | Role | OS | IP Address | Status |
| :--- | :--- | :--- | :--- | :--- |
| **k8s-control** | Control Plane | CentOS 9 Stream | 192.168.0.50 | **Hardened** |
| **k8s-worker-1** | Worker | CentOS 9 Stream | 192.168.0.60 | **Running** |
| **k8s-worker-2** | Worker | CentOS 9 Stream | 192.168.0.61 | **Running** |

---

### Next Step
I am currently transitioning to the **CKA Orchestration Layer**, where I will bootstrap the cluster using `kubeadm` and implement CNI-based networking.
