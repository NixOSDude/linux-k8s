# Keystone Project: Hardened Private Cloud Infrastructure

## Project Overview
This repository documents the implementation of a production-grade infrastructure foundation hosted on a dedicated **Dell Latitude E5570** bare-metal server. I engineered this environment to demonstrate advanced Linux system administration, proactive security hardening, and the implementation of the latest **NIST Post-Quantum Cryptographic (PQC)** standards.

---

## Phase I: Bare-Metal Infrastructure & Host Layer

### Dedicated Infrastructure Host (The Foundation)
* **Hardware:** Dell Latitude E5570
* **Processor:** Intel Core i7-6820HQ (4 Cores / 8 Threads @ 2.70GHz)
* **Memory:** 24.0 GiB DDR4
* **Primary Storage:** 1TB Western Digital SATA SSD
* **OS:** RHEL 9 (Registered Bare-Metal)
* **Role:** Dedicated KVM Hypervisor and Private Cloud Controller

### Networking & Identity
I established a consistent system identity to ensure cryptographic validity across the local network:
* **Hostname:** lfcs-hypervisor.local
* **Network Role:** Linux Bridge controller for internal K8s VPC simulation.

---

## Phase II: Post-Quantum Security & Identity Layer

### Post-Quantum Cryptography (PQC) Integration
To future-proof the infrastructure against quantum threats, I integrated the **Open Quantum Safe (OQS)** provider into the OpenSSL 3.x stack.

* **Hybrid Implementation:** I provisioned both **ML-KEM-768** (for secure key encapsulation) and **ML-DSA-65** (for digital signatures).
* **Identity Generation:** I generated a quantum-safe private key and a self-signed **X.509 Certificate** for the hypervisor using the **ML-DSA-65** algorithm (NIST FIPS 204).
* **Cryptographic Verification:** I verified the certificate's integrity and signature using `openssl x509`, confirming a valid 7.4K lattice-based signature.

### Repository Security & Hardening
I implemented strict data exfiltration prevention for the project repository:
* **Credential Protection:** Configured `.gitignore` with wildcard patterns (`*.key`) to prevent the accidental commitment of sensitive private key material.
* **SELinux Enforcement:** All service directories are restricted via `httpd_sys_content_t` or equivalent mandatory access control contexts.

---

## Current Infrastructure Status

| Component | Role | OS | Status | Verified Tech |
| :--- | :--- | :--- | :--- | :--- |
| **LFCS-Hypervisor** | Bare-Metal Host | RHEL 9 | **Hardened** | ML-DSA-65 / ML-KEM-768 |
| **Storage Layer** | Volume Management | RHEL 9 | **Provisioning** | LVM Physical Volumes |
| **Identity Layer** | TLS/SSL | X.509 | **Verified** | Quantum-Safe Certs |
