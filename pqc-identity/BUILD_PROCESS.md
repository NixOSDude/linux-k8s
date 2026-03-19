# Source Compilation & Build Process: Cryptographic Stack

## Overview
To enable NIST Post-Quantum Cryptography (PQC) standards (ML-KEM and ML-DSA) on my RHEL 9 bare-metal host, I was required to compile the underlying cryptographic libraries from source. Because standard package repositories do not yet provide native integration for these bleeding-edge algorithms, I utilized a complete C build toolchain to construct and integrate the Open Quantum Safe (OQS) provider directly into the system's OpenSSL environment.

## Build Environment & Toolchain
* **Host CPU:** Intel Core i7-6820HQ (4 Cores / 8 Threads)
* **Compiler:** GCC (GNU Compiler Collection)
* **Build Systems:** CMake, Make, Ninja
* **Parallelism:** I specifically utilized `ninja -j4` to distribute compilation tasks across available CPU threads, maximizing resource efficiency and significantly reducing overall build time.

---

## Implementation Log

### 1. Provisioning Build Dependencies
I began by installing the necessary development headers and build tools via RHEL's native package manager (`dnf`).
```bash
sudo dnf groupinstall "Development Tools" -y
sudo dnf install cmake ninja-build openssl-devel -y
```

### 2. Compiling the Core Quantum-Safe Library (`liboqs`)
I cloned the upstream `liboqs` repository and configured the build system. Using Ninja, I compiled the library from source. Because RHEL 9 does not automatically track custom library paths by default, I explicitly created a configuration file in `/etc/ld.so.conf.d/` for the dynamic linker to locate `liboqs.so.9` before rebuilding the shared library cache.
```bash
git clone [https://github.com/open-quantum-safe/liboqs.git](https://github.com/open-quantum-safe/liboqs.git)
cd liboqs
mkdir build && cd build
cmake -G"Ninja" ..
ninja -j4
sudo ninja install

# Configure dynamic linker for the custom liboqs library path
echo "/usr/local/lib64" | sudo tee /etc/ld.so.conf.d/liboqs.conf
sudo ldconfig
```

### 3. Compiling the OpenSSL Provider (`oqs-provider`)
With the core C library installed and explicitly cached, I built the OpenSSL provider wrapper to expose these lattice-based algorithms to standard system binaries.
```bash
git clone [https://github.com/open-quantum-safe/oqs-provider.git](https://github.com/open-quantum-safe/oqs-provider.git)
cd oqs-provider
mkdir build && cd build
cmake -DOPENSSL_ROOT_DIR=/usr -G"Ninja" ..
ninja -j4
sudo make install
```

### 4. OpenSSL Subsystem Integration
To complete the build process, I modified the system's OpenSSL configuration to dynamically load the newly compiled provider, verifying success with the `openssl list -providers` command.
```bash
# Linked the compiled oqsprovider.so into the OpenSSL modules directory
# Modified openssl.cnf to activate the 'oqsprovider' globally
```
