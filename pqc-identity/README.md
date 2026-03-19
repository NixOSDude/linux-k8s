# Keystone Project: Hardened Private Cloud Infrastructure

## Project Overview
This repository documents the implementation of a production-grade infrastructure foundation hosted on a dedicated **Dell Latitude E5570** bare-metal server running **RHEL 9**. I engineered this environment to demonstrate advanced Linux system administration and the practical application of **NIST Post-Quantum Cryptographic (PQC)** standards.

## Methodology: Discovery & Implementation
My implementation followed a documentation-driven, iterative approach to integrate cutting-edge cryptographic providers:

* **Documentation-First Discovery:** I utilized native Linux `man` pages and OpenSSL `help` flags to navigate the complex sub-command structures required for the OQS (Open Quantum Safe) provider. 📖
* **Iterative Command Refinement:** Through a cycle of trial and verification, I narrowed down the precise syntax for generating Lattice-based keys and certificates, ensuring full compatibility with RHEL 9's security subsystem. ⚙️
* **Algorithm Validation:** I verified the system's support for NIST-standardized algorithms, specifically **ML-KEM-768** (for key encapsulation) and **ML-DSA-65** (for digital signatures), by filtering the OpenSSL algorithm list and provider status. 🔍

---

## Phase I: Bare-Metal Infrastructure & Host Layer

### Dedicated Infrastructure Host
* **Hardware:** Dell Latitude E5570 (Intel Core i7-6820HQ)
* **Memory:** 24.0 GiB DDR4
* **Primary Storage:** 1TB Western Digital SATA SSD
* **OS:** RHEL 9 (Registered Bare-Metal)

---

## Phase II: Post-Quantum Security & Identity Layer

### PQC Identity Generation
I established a future-proof identity layer by provisioning the following artifacts:
1.  **ML-KEM-768 Handshake Key:** Established for secure, quantum-resistant key exchange.
2.  **ML-DSA-65 Signature Key:** Provisioned for high-integrity digital signatures.
3.  **X.509 Certificate:** A self-signed certificate for `lfcs-hypervisor.local` utilizing a 7.4K lattice-based signature.

### Repository Hardening
I implemented a strict "No Secrets" policy for this public portfolio:
* **Credential Protection:** `.gitignore` utilizes wildcard patterns (`*.key`) to ensure private material is never staged.
* **SELinux Policy:** Custom service directories are secured with the appropriate mandatory access control contexts.

---

## Infrastructure Deployment Log
Following the methodology described above, I executed the following "Golden Path" to provision the identity layer:

### 1. Key Generation
```bash
# Signature Key (ML-DSA-65)
openssl genpkey -provider default -algorithm ML-DSA-65 -out nist_std_dsa.key

# Handshake Key (ML-KEM-768)
openssl genpkey -provider default -algorithm ML-KEM-768 -out nist_std.key
\```

### 2. Certificate Signing Request (CSR)
```bash
openssl req -new -key nist_std_dsa.key -out nist_std.csr
\```

### 3. Certificate Issuance (Self-Signing)
```bash
openssl x509 -req -in nist_std.csr -signkey nist_std_dsa.key -days 365 -out quantum_server.crt
\```

---

## Technical Proof (Evidence)
```text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            42:78:97:58:d6:c9:62:12:f9:75:e0:07:18:03:35:df:95:59:e2:cf
        Signature Algorithm: ML-DSA-65
        Issuer: C=XX, L=Default City, O=Default Company Ltd
        Validity
            Not Before: Mar 19 20:03:59 2026 GMT
            Not After : Mar 19 20:03:59 2027 GMT
        Subject: C=XX, L=Default City, O=Default Company Ltd
        Subject Public Key Info:
            Public Key Algorithm: ML-DSA-65
                ML-DSA-65 Public-Key:
                pub:
                    64:80:ba:84:d9:1f:ac:c1:ef:2a:23:7f:06:aa:07:
                    79:37:b8:e9:50:6f:95:14:f4:83:fd:b6:a5:7f:0f:
\```

---

## Verification Guide
To verify the authenticity and algorithm of this certificate, execute:
```bash
openssl x509 -text -noout -in quantum_server.crt
\```
**Audit points:** Confirm `Signature Algorithm: ML-DSA-65` and `Public Key Algorithm: ML-DSA-65`.
