
# Hardware Inventory — ThinkCentre M91p

## 1. Overview

This document records the hardware used in my home lab for Linux system administration, networking, Docker, and infrastructure experimentation.

The objective is to understand the machine's capabilities, document its limitations, and maintain an accurate record of hardware changes.

## 2. System Specifications

| Component | Specification |
|---|---|
| Manufacturer | Lenovo |
| Model | ThinkCentre M91p |
| Form Factor | To be verified |
| Processor | To be verified |
| CPU Cores / Threads | To be verified |
| Installed RAM | Approximately 4–8 GB; verify exact amount |
| RAM Type | To be verified |
| Primary Storage | HDD; capacity to be verified |
| Additional Storage | To be verified |
| Graphics | To be verified |
| Network Interface | To be verified |
| Operating System | Linux Mint |
| Container Runtime | Docker |
| Network Connection | Home router |

**Note:** Only confirmed hardware details should be treated as final specifications.

## 3. Intended Workloads

The machine is being used for:

- Linux administration and troubleshooting.
- Docker containers and self-hosted services.
- IP addressing, DNS, routing, and connectivity experiments.
- Bash scripting and system monitoring.
- Lightweight infrastructure projects.

## 4. Hardware Verification

Run the following commands on the ThinkCentre to collect the actual specifications.

### Operating System

```bash
hostnamectl
cat /etc/os-release
uname -r
```

### Processor

```bash
lscpu
```

### Memory

```bash
free -h
sudo dmidecode -t memory
```

### Storage

```bash
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS,MODEL
df -h
```

### Network Interfaces

```bash
ip -br addr
ip route
```

### Graphics and PCI Devices

```bash
lspci
```

### Docker

```bash
docker version
docker ps
docker system df
```

Some commands may require additional packages or administrator permissions. Review commands before running them.

## 5. Resource Constraints

The system has a limited memory budget, so workloads should be selected accordingly.

Current considerations:

- Prefer lightweight Linux services.
- Avoid running too many memory-intensive containers simultaneously.
- Monitor RAM and swap usage during deployments.
- Check available disk space before downloading images.
- Avoid unnecessary virtual machines until available resources are confirmed.
- Keep backups of important application data.

## 6. Network Environment

The ThinkCentre is connected to a home router.

The following details should be recorded after verification:

| Property | Value |
|---|---|
| Connection Type | To be verified |
| IP Address | Keep private unless safe to publish |
| Subnet / Prefix | To be verified |
| Default Gateway | To be verified |
| DNS Resolver | To be verified |
| DHCP or Static Address | To be verified |

Do not publish public IP addresses, router credentials, Wi-Fi passwords, or other sensitive network information.

## 7. Future Hardware Improvements

Potential upgrades will be considered only when necessary:

- Increasing RAM if workloads require it.
- Improving storage capacity or reliability.
- Adding a managed switch for VLAN experiments.
- Adding a separate access point or router for advanced network labs.

The current goal is to maximize learning with existing hardware and avoid unnecessary expenses.

## 8. Maintenance Log

| Date | Change | Reason | Result |
|---|---|---|---|
| YYYY-MM-DD | Initial inventory | Document home lab hardware | Pending verification |

Update this table whenever hardware is added, removed, or replaced.

## 9. Status

**Inventory status:** Partially documented.

The model and operating system are known. Processor, exact RAM capacity, storage details, and network configuration remain to be verified.

---

*Part of the ThinkCentre Home Lab portfolio.*
