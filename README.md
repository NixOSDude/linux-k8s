# Keystone K8s Project: Production-Grade Private Cloud

## Project Overview
This repository documents the implementation of a production-grade private cloud environment hosted on bare-metal hardware. I designed this infrastructure to demonstrate advanced Linux system administration, storage orchestration, and virtualized networking. 

The goal is to move from raw hardware to a fully functional Kubernetes cluster using a "no-bloat" infrastructure-as-code approach.

## Phase I: Bare-Metal Infrastructure Layer

### Host Specifications
* **Model:** Dell Latitude E5570
* **Processor:** Intel Core i7-6820HQ (4 Cores / 8 Threads)
* **Memory:** 24GiB DDR4 (16GiB + 8GiB)
* **Storage:** 1TB Western Digital SATA SSD
* **Hypervisor:** KVM/libvirt on Ubuntu 22.04 LTS

### Storage Orchestration (LVM)
I utilized **Logical Volume Management (LVM)** to provide a flexible and high-performance storage backend for the cluster nodes.

1. **Initialize Physical Volume:**
   ```bash
   sudo pvcreate /dev/sda3
   ```
2. **Create Volume Group:**
   ```bash
   sudo vgcreate vg_infrastructure /dev/sda3
   ```
3. **Provision Logical Volumes:**
   I allocated 50GiB per node to ensure sufficient overhead for container images and persistent volumes.
   ```bash
   sudo lvcreate -L 50G -n lv_k8s_control_plane vg_infrastructure
   sudo lvcreate -L 50G -n lv_k8s_worker_1 vg_infrastructure
   sudo lvcreate -L 50G -n lv_k8s_worker_2 vg_infrastructure
   ```

### Network Architecture
I implemented a static IP schema to ensure predictable node communication and cluster stability. I used **Netplan** to bridge the virtual interfaces to the physical network.

**Configuration:** `/etc/netplan/00-installer-config.yaml`
```yaml
network:
  version: 2
  ethernets:
    enp0s31f6:
      dhcp4: no
      addresses: [192.168.0.100/24]
      routes:
        - to: default
          via: 192.168.0.1
      nameservers:
        addresses: [8.8.8.8, 1.1.1.1]
```

---

## Phase II: Automated Node Provisioning
To streamline the deployment of the worker nodes, I used **virt-customize** to perform "offline surgery" on the VM disk images. This allows for immediate identity assignment (hostname and networking) without manual console intervention.

### Injection Workflow
I used a Bash-driven automation pattern to inject specific Netplan configurations and set hostnames:

```bash
sudo virt-customize -d k8s-worker-2 \
  --hostname k8s-worker-2 \
  --upload worker2-net.yaml:/etc/netplan/00-installer-config.yaml
```

## Current Cluster Status
I have successfully provisioned and verified the following nodes:

| Node Name | Role | IP Address | Status |
| :--- | :--- | :--- | :--- |
| k8s-control-plane | Control Plane | 192.168.0.50 | Running |
| k8s-worker-1 | Worker | 192.168.0.60 | Running |
| k8s-worker-2 | Worker | 192.168.0.61 | Running |

