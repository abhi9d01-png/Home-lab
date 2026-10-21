
# 🖥️ ThinkCentre Home Lab

**A hands-on infrastructure lab for Linux, networking, Docker, and system administration.**

I'm building a home lab using a Lenovo ThinkCentre M91p to develop practical infrastructure engineering skills through experimentation, deployment, troubleshooting, and documentation.

The goal is to understand how real systems work, document what I learn, and build reproducible projects that demonstrate my technical skills.

---

## 🎯 Objectives

- Learn Linux system administration and service management.
- Understand IP addressing, subnetting, DNS, DHCP, and routing.
- Deploy and manage applications using Docker and Docker Compose.
- Practise network diagnostics and infrastructure troubleshooting.
- Explore system security, monitoring, and automation.
- Document projects and configurations for reproducibility.

## 🖥️ Hardware & Environment

| Component | Details |
|---|---|
| System | Lenovo ThinkCentre M91p |
| Operating System | Linux Mint |
| RAM | 4–8 GB (exact capacity to be verified) |
| Storage | To be documented |
| Network | Home router |
| Containerization | Docker / Docker Compose |
| Budget | Existing hardware and free software |

*Hardware specifications and installed services will be verified and updated as the lab develops.*

## 🏗️ Architecture

The current setup consists of a physical Linux machine connected to my home network.

```text
              Internet
                  |
             Home Router
                  |
             Local Network
                  |
        Lenovo ThinkCentre M91p
                  |
              Linux Mint
                  |
          Docker / Services
                  |
       Applications and Testing
```

The diagram represents the intended high-level layout. Actual services, network interfaces, and container connections will be documented as they are verified.

## 📂 Repository Structure

```text
thinkcentre-homelab/
├── README.md
├── docs/
│   ├── hardware-inventory.md
│   └── setup-notes.md
├── diagrams/
│   └── home-network.png
├── projects/
│   ├── 01-linux-server/
│   │   └── README.md
│   ├── 02-docker-stack/
│   │   └── README.md
│   └── 03-network-diagnostics/
│       └── README.md
├── scripts/
│   └── README.md
└── .gitignore
```

## 🚀 Projects

### 01. Linux Server Administration

**Focus:** Linux fundamentals and system management.

Planned documentation and experiments:

- Hardware and operating system inventory.
- CPU, RAM, disk, and system health checks.
- Service management and log analysis.
- User permissions and SSH configuration.
- Troubleshooting and recovery procedures.

**Status:** Documentation and development in progress.

### 02. Docker Infrastructure

**Focus:** Containerization and reproducible deployments.

Planned documentation and experiments:

- Inventory of existing containers.
- Docker Compose deployment.
- Container networking and port mappings.
- Persistent volumes and application data.
- Service logs, restart behaviour, and validation.

**Status:** Existing services to be documented; reproducible deployment work ongoing.

### 03. Network Diagnostics

**Focus:** Practical networking fundamentals.

Planned documentation and experiments:

- IP addressing and subnetting.
- Default gateway and routing analysis.
- DNS resolution and connectivity tests.
- Network topology documentation.
- Troubleshooting common network failures.

**Status:** Planned.

## 🛠️ Tools & Technologies

- **Operating System:** Linux Mint
- **Containerization:** Docker, Docker Compose
- **Networking:** TCP/IP, DNS, DHCP, routing, subnetting
- **Scripting:** Bash
- **Documentation:** Markdown, Git, GitHub
- **Network Simulation:** Cisco Packet Tracer (as needed)

Technologies will be added to the implemented stack as they are actually used.

## 🔍 Example Diagnostic Commands

These commands help inspect the Linux system and network.

```bash
# Operating system and hardware summary
hostnamectl

# Memory usage
free -h

# Storage devices
lsblk

# Disk usage
df -h

# Network interfaces
ip -br addr

# Routing table
ip route

# Check failed system services
systemctl --failed

# List running Docker containers
docker ps
```

Commands and results will be documented alongside the relevant projects.

## 🔐 Security Principles

- Never commit passwords, access tokens, or private keys.
- Exclude sensitive environment files using `.gitignore`.
- Review configurations before publishing.
- Avoid unnecessary exposure of services to the public internet.
- Use least-privilege permissions wherever practical.
- Test changes before applying them to running services.

## 📚 Learning & Documentation

Each project aims to include:

- Problem statement and objectives.
- Architecture and configuration.
- Implementation steps.
- Testing and verification results.
- Troubleshooting notes.
- Lessons learned and future improvements.

## 🗺️ Roadmap

- [ ] Document the exact hardware specifications.
- [ ] Create a verified network topology diagram.
- [ ] Document Linux services and system health.
- [ ] Publish a reproducible Docker Compose deployment.
- [ ] Test container data persistence and recovery.
- [ ] Document DNS and connectivity troubleshooting.
- [ ] Add useful Bash automation scripts.
- [ ] Explore monitoring and backup strategies.
- [ ] Publish project walkthroughs and lessons learned.

## 📈 Progress

This is an evolving personal learning project. Project status, configurations, and results will be updated as implementations are completed and verified.

## 👨‍💻 About

I'm an IT engineering student developing practical skills in networking, Linux administration, Docker, and infrastructure engineering.

This home lab is my way of learning by building, troubleshooting, testing, and documenting real systems.

**Learning by doing. Documenting every step. Improving continuously.**

---

⭐ Follow the repository to see the lab evolve as new projects are implemented.
