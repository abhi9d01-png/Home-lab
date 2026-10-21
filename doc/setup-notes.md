
# Setup Notes — ThinkCentre Home Lab

## 1. Overview

This document records the installation, configuration, and maintenance of my home lab running on a Lenovo ThinkCentre M91p.

The system is used to develop practical skills in:

- Linux system administration
- Networking and connectivity troubleshooting
- Docker and container management
- Infrastructure monitoring
- Bash scripting and automation

The goal is to maintain a reproducible setup and document important configuration changes.

## 2. Hardware and Environment

| Component | Details |
|---|---|
| Machine | Lenovo ThinkCentre M91p |
| Operating System | Linux Mint |
| RAM | 4–8 GB; exact capacity to be verified |
| Storage | Internal HDD; capacity to be verified |
| Network | Home router |
| Container Runtime | Docker |
| Primary Purpose | Networking, Linux, and Docker lab |

Refer to [`hardware-inventory.md`](hardware-inventory.md) for detailed hardware specifications.

## 3. Initial System Verification

After booting Linux Mint, verify the operating system and hardware.

```bash
hostnamectl
cat /etc/os-release
uname -r
lscpu
free -h
lsblk
df -h
```

Record the command output in your private notes and document relevant findings here.

### Verification checklist

- [ ] Linux Mint boots successfully from internal storage.
- [ ] Hardware specifications are recorded.
- [ ] Storage devices and mounted filesystems are identified.
- [ ] Network connectivity is working.
- [ ] System updates have been checked.
- [ ] Important existing data is backed up.

## 4. System Updates

Check for available package updates.

```bash
sudo apt update
apt list --upgradable
```

Review the available updates before installing them.

```bash
sudo apt upgrade
```

Record the date, any important changes, and whether a reboot was required.

**Note:** Do not perform a distribution upgrade or major operating system change without first checking compatibility and backing up important data.

## 5. Network Configuration

The ThinkCentre connects to an existing home router.

Inspect the network interfaces:

```bash
ip -br addr
```

Inspect the routing table:

```bash
ip route
```

Check DNS resolution:

```bash
getent hosts github.com
```

Test IP connectivity:

```bash
ping -c 4 1.1.1.1
```

### Network details

| Property | Value |
|---|---|
| Connection Type | To be verified |
| IP Address | To be documented |
| Subnet / Prefix | To be documented |
| Default Gateway | To be documented |
| DNS Resolver | To be documented |
| Address Assignment | DHCP or static; to be verified |

Do not publish sensitive network details or credentials.

## 6. Docker Environment

Docker is part of the home lab environment. Existing containers and services must be inventoried before making configuration changes.

### Check Docker installation

```bash
docker --version
docker compose version
docker info
```

### Inspect existing containers

```bash
docker ps
docker ps -a
docker network ls
docker volume ls
docker system df
```

### Review a container's logs

```bash
docker logs --tail 100 CONTAINER_NAME
```

Replace `CONTAINER_NAME` with the actual container name.

### Docker management principles

- Prefer Docker Compose for reproducible deployments.
- Use persistent volumes for application data when appropriate.
- Document container ports and network connections.
- Avoid exposing services publicly without a security review.
- Never delete volumes or prune resources without checking what data they contain.
- Keep secrets out of GitHub.

## 7. Remote Administration

SSH may be used to administer the ThinkCentre from another device on the local network.

Before enabling SSH:

1. Confirm that remote access is required.
2. Review the existing SSH configuration.
3. Install and enable an SSH server only if necessary.
4. Prefer key-based authentication where practical.
5. Restrict access to trusted devices and networks.

Record the actual SSH configuration and verification results after setup.

Do not publish private keys, passwords, or sensitive connection details.

## 8. Directory Structure

The repository is organized as follows:

```text
thinkcentre-homelab/
├── README.md
├── docs/
│   ├── hardware-inventory.md
│   └── setup-notes.md
├── diagrams/
├── projects/
│   ├── 01-linux-server/
│   ├── 02-docker-stack/
│   └── 03-network-diagnostics/
├── scripts/
└── .gitignore
```

Project-specific configuration, test results, and implementation details should be stored in their respective project directories.

## 9. Troubleshooting Log

Document problems systematically instead of recording only the final fix.

| Date | Issue | Diagnosis | Resolution | Status |
|---|---|---|---|---|
| YYYY-MM-DD | To be documented | To be documented | To be documented | Pending |

For each incident, record:

- The original symptoms.
- Relevant logs and command output.
- The suspected cause.
- The troubleshooting steps taken.
- The final fix, if confirmed.
- The test used to verify the outcome.

## 10. Backup and Recovery

Before major system or Docker changes:

- Identify important configuration files.
- Back up application data and persistent volumes.
- Store backup copies on a separate, reliable destination.
- Document restoration steps.
- Test recovery before depending on the backup.

Do not treat a backup as verified until a restoration test succeeds.

## 11. Current Status

- Operating system: Linux Mint.
- Hardware: Lenovo ThinkCentre M91p.
- Networking: Home router.
- Docker: Existing containers or services are reported to be running.
- Documentation: In progress.

Exact hardware specifications, current services, network settings, and successful verification results will be added as they are inspected.

## 12. Next Steps

- [ ] Verify hardware and operating system details.
- [ ] Record current network configuration.
- [ ] Inventory Docker containers and volumes.
- [ ] Document existing services and their purposes.
- [ ] Complete the Linux administration project.
- [ ] Deploy and test a reproducible Docker Compose project.
- [ ] Create a network topology diagram.
- [ ] Publish sanitized evidence and troubleshooting case studies.

---

*Part of the ThinkCentre Home Lab infrastructure portfolio.*
