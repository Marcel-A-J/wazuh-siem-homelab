# Enterprise SIEM Deployment & Threat Detection Lab (Wazuh on Proxmox VE)

![Proxmox](https://img.shields.io/badge/Proxmox_VE-E57000?style=for-the-badge&logo=proxmox&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu_Server-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Wazuh](https://img.shields.io/badge/Wazuh_SIEM-00A4E4?style=for-the-badge&logo=wazuh&logoColor=white)
![Raspberry Pi](https://img.shields.io/badge/Raspberry_Pi_ARM64-C51A4A?style=for-the-badge&logo=raspberry-pi&logoColor=white)

An end-to-end implementation of an **Enterprise Security Information and Event Management (SIEM)** environment using **Wazuh 4.x** and **OpenSearch**, deployed as a dedicated KVM Virtual Machine on a Bare-Metal Proxmox Hypervisor.

This project demonstrates practical skills in **infrastructure deployment, cross-architecture log collection (x86_64 to aarch64), real-time brute-force threat detection, and automated vulnerability management (SCA/CVE)**.

---

## 🏗️ Architecture & Topology

- **Hypervisor:** Proxmox VE (x86_64, bare-metal laptop node with native UPS/battery redundancy)
- **SIEM Server (KVM):** Ubuntu Server 24.04 LTS (4 vCPUs, 6 GB RAM, 38 GB LVM storage)
  - **Wazuh Manager:** Core engine for log decoding and rule processing
  - **Wazuh Indexer:** High-performance OpenSearch database cluster
  - **Wazuh Dashboard:** Web-based SOC visualization portal (HTTPS / Port 443)
- **Monitored Endpoint (Agent):** Raspberry Pi 3B+ (ARM64 / Debian Linux)

![Proxmox Virtual Machine Setup](screenshots/00_proxmox_vm_running.png)

---

## 🚀 Deployment & Engineering Highlights

### 1. Server Provisioning & Storage Troubleshooting
The Wazuh server stack requires substantial disk space for the initial package extraction and OpenSearch indices. During deployment, the default Ubuntu LVM volume allocation caused an initial extraction crash (`disk full error`).
- **Resolution:** Resized the logical volume and extended the ext4 filesystem dynamically during runtime:
  ```bash
  sudo lvextend -l +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
  sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
