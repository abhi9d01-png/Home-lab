**
# ThinkCentre Home Lab

A practical home lab built using a Lenovo ThinkCentre M91p to learn Linux system administration, networking, Docker, DNS, and self-hosted infrastructure.

This repository documents the lab's architecture, services, configuration, deployment process, troubleshooting, testing, and future improvements.

> **Status:** Work in progress. Some configurations have been tested, while other components still require verification.

## Table of Contents

- [Overview](#overview)
- [Objectives](#objectives)
- [Hardware and Software](#hardware-and-software)
- [Network Configuration](#network-configuration)
- [Architecture](#architecture)
- [Repository Structure](#repository-structure)
- [Projects](#projects)
- [Pi-hole DNS Filtering](#pi-hole-dns-filtering)
- [Deployment](#deployment)
- [DNS Request Flow](#dns-request-flow)
- [Networking and DHCP](#networking-and-dhcp)
- [Troubleshooting](#troubleshooting)
- [Testing and Verification](#testing-and-verification)
- [Security](#security)
- [Useful Commands](#useful-commands)
- [Known Issues](#known-issues)
- [Roadmap](#roadmap)
- [Skills Demonstrated](#skills-demonstrated)

---

## Overview

The ThinkCentre Home Lab uses an existing desktop computer as a small server for hands-on infrastructure experimentation.

The main focus is to understand how services are deployed, how devices communicate over a network, how DNS requests are resolved, and how to troubleshoot real problems.

Instead of treating the server as a one-time setup, this project follows an iterative approach:

1. Configure the host operating system.
2. Establish and document network connectivity.
3. Deploy services using Docker.
4. Test each service independently.
5. Document errors and their solutions.
6. Improve reliability, security, and monitoring.

## Objectives

- Learn Linux server administration.
- Understand IP addressing, subnetting, DNS, DHCP, and routing.
- Deploy applications using Docker and Docker Compose.
- Manage containerized services through Dockge.
- Configure Pi-hole for DNS-based filtering.
- Troubleshoot DNS, network, and container issues.
- Maintain version-controlled technical documentation.
- Build a portfolio demonstrating practical infrastructure skills.

## Hardware and Software

### Hardware

| Component | Details |
|---|---|
| Host machine | Lenovo ThinkCentre M91p |
| Host operating system | Linux Mint, as recorded in the existing project documentation |
| Router | TP-Link EX220-G2u v1 |
| Server IP observed during setup | `192.168.0.119` |
| Default gateway observed during setup | `192.168.0.1` |
| RAM | Exact capacity not yet verified |
| Storage | Capacity and layout not yet documented |

### Software and Tools

| Technology | Purpose |
|---|---|
| Linux Mint | Host operating system |
| Docker | Container runtime |
| Docker Compose | Service deployment and configuration |
| Dockge | Web interface for managing Compose stacks |
| Pi-hole | DNS filtering |
| Git | Version control |
| GitHub | Repository hosting |
| Bash | Administration and automation |
| Markdown | Technical documentation |
| Cisco Packet Tracer | Optional network simulations |

## Network Configuration

The following values were observed during the Pi-hole setup process. They must be checked again before being treated as the current configuration.

| Setting | Observed value |
|---|---|
| Router/default gateway | `192.168.0.1` |
| Pi-hole host | `192.168.0.119` |
| Subnet mask | `255.255.255.0` |
| LAN subnet | `192.168.0.0/24` |
| Previously observed router DHCP pool | Approximately `192.168.0.100`–`192.168.0.240` |
| Pi-hole DHCP range previously configured | `192.168.0.150`–`192.168.0.240` |
| Pi-hole dashboard | `http://192.168.0.119/admin` |

**Important:** Keep the Pi-hole host IP stable. Use a router DHCP reservation or a properly configured static address outside the router's dynamically assigned range.

The exact current DHCP configuration must be verified before changing network settings.

## Architecture

The intended architecture is:

```text
                  Internet
                     |
                     |
              Home Router
             192.168.0.1
                     |
              Home LAN / Wi-Fi
                     |
          +----------+----------+
          |                     |
       Clients             Home Server
                            ThinkCentre
                         192.168.0.119
                                |
                              Docker
                                |
                             Pi-hole
                                |
                         DNS Filtering
                                |
                       Configured Upstream
                           DNS Resolver
```

This is a logical overview. Actual connectivity depends on the current Docker network mode, router configuration, and Pi-hole DNS settings.

## Repository Structure

The repository uses the following main directories:

```text
Home-lab/
├── README.md
├── .gitignore
├── diagrams/
├── doc/
├── projects/
└── scripts/
```

Suggested organization for service-specific documentation:

```text
projects/
└── 04-pi-hole/
    ├── README.md
    ├── troubleshooting.md
    └── test-results.md
```

Create these service-specific files if they do not already exist. Keep the paths in this README consistent with the actual repository structure.

## Projects

### 1. Linux Server Administration

**Goal:** Understand and maintain the Linux host used for the home lab.

Areas of work:

- Inspect system hardware and operating system information.
- Manage files, permissions, users, and groups.
- Inspect running processes and services.
- Review system logs.
- Install updates and maintain the host.
- Check disk usage and memory consumption.
- Diagnose connectivity problems.

Potential future additions:

- Automated system health checks.
- Scheduled maintenance scripts.
- Backup and recovery documentation.

### 2. Docker Infrastructure

**Goal:** Run applications as manageable, reproducible containers.

Areas of work:

- Install and verify Docker.
- Deploy applications with Docker Compose.
- Manage containers through Dockge.
- Configure persistent volumes.
- Configure container networking.
- Inspect logs and container health.
- Restart and update services safely.

Important concepts:

- Images versus containers.
- Compose services and environment variables.
- Bind mounts versus named volumes.
- Bridge networking versus host networking.
- Port publishing and service discovery.
- Restart policies and persistent application data.

### 3. Pi-hole DNS Filtering

**Goal:** Deploy Pi-hole to filter DNS requests and understand how network-wide DNS filtering works.

Pi-hole provides:

- DNS query processing.
- Domain blocklists.
- Query statistics.
- Client and domain visibility.
- A web administration interface.
- Configurable upstream DNS resolvers.

Observed dashboard address:

`http://192.168.0.119/admin`

The dashboard is intended for use on the trusted home network. Its accessibility should be verified after host or container networking changes.

## Pi-hole DNS Filtering

### Project Goals

- Deploy Pi-hole with Docker.
- Access its administration dashboard.
- Resolve DNS queries through the Pi-hole server.
- Observe queries in the dashboard.
- Configure and verify domain blocklists.
- Preserve configuration across container restarts.
- Document DNS and DHCP responsibilities.
- Avoid disrupting connectivity for other devices.

### Deployment Decisions

| Decision | Current understanding |
|---|---|
| Application | Pi-hole |
| Deployment method | Docker Compose managed through Dockge |
| Host | Lenovo ThinkCentre M91p |
| Persistent configuration | `./etc-pihole` |
| Additional configuration directory | `./etc-dnsmasq.d` |
| Time zone | `Asia/Kolkata` |
| Network modes considered | Docker bridge and host |
| Current final network mode | Must be verified |
| DHCP responsibility | Must be verified before enabling Pi-hole DHCP |

### Persistent Data

The following directories were used in the Compose configuration:

```text
./etc-pihole:/etc/pihole
./etc-dnsmasq.d:/etc/dnsmasq.d
```

These bind mounts are intended to preserve Pi-hole configuration when the container is recreated.

Back up these directories before making major changes.

### Blocklists

The project initially used the default StevenBlack list.

A HaGeZi blocklist URL was also attempted, but successful downloading and gravity processing were not confirmed.

Before adding a blocklist:

1. Confirm that the URL is valid.
2. Add it through the Pi-hole administration interface.
3. Update gravity.
4. Check the processing output for errors.
5. Test a known blocked domain.
6. Confirm that ordinary domains continue to resolve.

Do not assume that a list is active merely because its URL was entered.

## Deployment

The following Compose example represents an intended host-networking configuration. Verify it against the installed Pi-hole version, current settings, and existing container before using it.

### Example `compose.yaml`

```yaml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    restart: unless-stopped

    network_mode: host

    environment:
      TZ: "Asia/Kolkata"
      FTLCONF_dns_listeningMode: "ALL"
      FTLCONF_webserver_api_password: "${PIHOLE_WEB_PASSWORD:?Set PIHOLE_WEB_PASSWORD in .env}"

    volumes:
      - "./etc-pihole:/etc/pihole"
      - "./etc-dnsmasq.d:/etc/dnsmasq.d"

    cap_add:
      - NET_ADMIN
```

### Important deployment notes

- Host networking does not use a `ports:` section.
- The host's ports must be available for Pi-hole to bind to them.
- Do not run multiple DNS services on the same address and port without understanding the conflict.
- Protect the dashboard with a strong password.
- Verify the supported environment variables for the installed Pi-hole release.
- Back up existing configuration before replacing a working stack.
- Do not switch network modes without checking the current configuration and expected impact.

### Environment file

Create a `.env` file in the stack directory:

```dotenv
PIHOLE_WEB_PASSWORD=replace_with_a_strong_password
```

Replace the example value with a unique password.

**Never commit the `.env` file or real credentials to GitHub.**

The actual deployment state must be confirmed using Docker inspection and DNS tests; this example does not prove that the service is currently running correctly.

## DNS Request Flow

When a client uses Pi-hole for DNS, the expected request flow is:

```text
Client Device
     |
     | DNS query
     v
Pi-hole
     |
     | Check configured rules and cache
     |
     +---- Blocked domain ----> Return filtering response
     |
     +---- Allowed domain ----> Upstream DNS resolver
                                      |
                                      v
                                  DNS response
                                      |
                                      v
                                  Pi-hole
                                      |
                                      v
                                  Client Device
```

Pi-hole filters DNS requests. It does not automatically block every advertisement, tracking method, or connection made directly to an IP address.

For network-wide filtering, client devices must actually use Pi-hole for DNS resolution.

## Networking and DHCP

### DNS and DHCP are different

- **DNS** translates domain names into IP addresses.
- **DHCP** assigns network settings, including IP addresses, to clients.

Pi-hole can provide DNS filtering without becoming the DHCP server.

### Safe DHCP policy

Until the final configuration is tested:

- Keep the router's DHCP server enabled.
- Keep Pi-hole's DHCP server disabled.
- Configure clients to use Pi-hole for DNS through a supported router DHCP setting or per-device configuration.

Only consider moving DHCP to Pi-hole if the router cannot provide the required DNS configuration and you have a tested recovery plan.

**Never run two independent DHCP servers on the same LAN unless the network has been deliberately designed to support that arrangement.** Conflicting DHCP responses can cause devices to receive incorrect network settings or lose connectivity.

If DHCP is deliberately migrated to Pi-hole:

1. Verify that Pi-hole is reachable at a stable IP address.
2. Record the router's existing DHCP settings.
3. Select a non-conflicting DHCP range.
4. Configure the correct gateway and subnet mask.
5. Disable the router DHCP server before enabling Pi-hole DHCP.
6. Test a client lease and DNS resolution.
7. Keep a recovery procedure available.

## Troubleshooting

### 1. Check container status

```bash
sudo docker ps --filter name=pihole
```

For stopped containers:

```bash
sudo docker ps -a --filter name=pihole
```

### 2. Inspect recent logs

```bash
sudo docker logs --tail 60 pihole
```

Look for:

- Port-binding errors.
- Configuration errors.
- DNS resolver failures.
- Permission problems.
- Restart loops.

### 3. Check Pi-hole status

```bash
sudo docker exec pihole pihole status
```

### 4. Check network mode

```bash
sudo docker inspect pihole --format '{{.HostConfig.NetworkMode}}'
```

The result should match the intended deployment. A host-networked container typically reports `host`.

### 5. Check published ports

```bash
sudo docker port pihole
```

In host networking mode, this command may show no published-port mappings. That alone does not mean the service is unavailable.

### 6. Test DNS resolution

Test a normal domain:

```bash
nslookup google.com 192.168.0.119
```

Test a domain expected to be blocked:

```bash
nslookup doubleclick.net 192.168.0.119
```

A failed lookup alone does not prove that a blocklist is working. Check the Pi-hole query log and inspect whether the response was blocked, refused, or failed for another reason.

### 7. Update Pi-hole gravity

```bash
sudo docker exec pihole pihole -g
```

Review the command output for download or processing failures.

### 8. Dashboard unavailable

Check:

1. Whether the container is running.
2. Whether Pi-hole logs report errors.
3. Whether the host IP is still `192.168.0.119`.
4. Whether another service occupies the expected web ports.
5. Whether the client and server can communicate on the LAN.
6. Whether the current network mode matches the Compose configuration.

### 9. Internet connectivity problems

Check the gateway first:

```bash
ping -c 4 192.168.0.1
```

Then test a public IP:

```bash
ping -c 4 1.1.1.1
```

Finally, test DNS:

```bash
nslookup google.com 192.168.0.119
```

These tests help distinguish local network, internet routing, and DNS resolution problems. A failed ping is not conclusive if the target blocks ICMP.

## Testing and Verification

Record actual results instead of marking a test as passed without evidence.

| ID | Test | Status |
|---|---|---|
| T01 | Pi-hole container exists | Verify |
| T02 | Pi-hole container remains running | Verify |
| T03 | Dashboard opens from a LAN client | Previously accessible; retest |
| T04 | Host IP remains stable | Verify |
| T05 | DNS query to Pi-hole succeeds | Previously succeeded for `google.com`; retest |
| T06 | Blocked domain is identified in query logs | Verify |
| T07 | Blocklist downloads successfully | Verify |
| T08 | Gravity update completes without errors | Verify |
| T09 | Configuration persists after container restart | Verify |
| T10 | Router DHCP and Pi-hole DHCP do not conflict | Verify |
| T11 | Normal browsing continues to work | Verify |
| T12 | Dashboard is protected by a strong password | Verify |

### Verification commands

```bash
sudo docker ps --filter name=pihole
sudo docker logs --tail 60 pihole
sudo docker exec pihole pihole status
sudo docker inspect pihole --format '{{.HostConfig.NetworkMode}}'
nslookup google.com 192.168.0.119
```

Update this table when each test is performed. Add the date, result, and relevant logs to a separate test-results document if needed.

## Security

Security practices for the lab:

- Use strong passwords for administrative interfaces.
- Never commit `.env` files, tokens, or credentials.
- Keep the operating system and container images updated.
- Review container logs and restart behavior.
- Avoid exposing the Pi-hole dashboard directly to the public internet.
- Limit administration to trusted devices and networks.
- Back up configuration before major changes.
- Review Docker permissions and capabilities.
- Document firewall and router changes.
- Keep a recovery procedure for DNS and DHCP changes.

### Suggested `.gitignore` entries

Check the existing `.gitignore` before modifying it.

```gitignore
# Secrets
.env
.env.*

# Pi-hole persistent data
etc-pihole/
etc-dnsmasq.d/

# Backups
*.backup
*.bak
backups/

# Temporary files
*.log
.DS_Store
Thumbs.db
```

Do not ignore the example Compose file or documentation. Review ignore rules carefully so that useful configuration templates remain version-controlled without exposing secrets or runtime data.

## Useful Commands

### Host information

```bash
hostnamectl
uname -a
free -h
df -h
ip address
ip route
```

### Docker management

```bash
sudo docker ps
sudo docker ps -a
sudo docker images
sudo docker compose version
sudo docker logs --tail 100 pihole
```

### Container resource usage

```bash
sudo docker stats --no-stream
```

### Network diagnostics

```bash
ip address
ip route
ping -c 4 192.168.0.1
nslookup google.com
nslookup google.com 192.168.0.119
```

Use administrative commands carefully. Do not remove volumes or delete persistent directories when troubleshooting unless you have a verified backup and understand the consequences.

## Known Issues and Pending Verification

The following items remain open until confirmed:

- Final Pi-hole network mode needs to be checked.
- Stable upstream DNS resolution needs to be verified.
- The attempted HaGeZi blocklist URL did not complete successfully during the earlier setup.
- Gravity update success needs confirmation.
- DNS filtering should be tested using query logs.
- The router's current DHCP state needs confirmation.
- Pi-hole DHCP should remain disabled unless deliberately migrated and tested.
- The final web-password configuration needs verification.
- Persistence should be tested after a container restart.
- Hardware RAM, storage, and exact software versions should be documented.
- Repository links should be checked against the actual files and folders.

## Roadmap

### Phase 1 — Host and networking

- [ ] Record exact hardware specifications.
- [ ] Document operating system and Docker versions.
- [ ] Confirm the host's stable IP address.
- [ ] Document the LAN subnet and gateway.
- [ ] Verify host internet connectivity.

### Phase 2 — Pi-hole

- [ ] Verify the container's final configuration.
- [ ] Confirm dashboard accessibility.
- [ ] Verify the administrative password.
- [ ] Confirm upstream DNS functionality.
- [ ] Test allowed and blocked domain queries.
- [ ] Verify blocklist downloads and gravity updates.
- [ ] Confirm persistent configuration.
- [ ] Document the final DHCP design.

### Phase 3 — Reliability

- [ ] Add a tested configuration backup procedure.
- [ ] Document restore steps.
- [ ] Record service health checks.
- [ ] Add a maintenance checklist.
- [ ] Document recovery from DNS failures.

### Phase 4 — Future infrastructure

- [ ] Add more self-hosted services when the current lab is stable.
- [ ] Explore monitoring and service health dashboards.
- [ ] Add Bash scripts for repeatable diagnostics.
- [ ] Create updated architecture diagrams.
- [ ] Document firewall rules and network segmentation as the lab evolves.

## Skills Demonstrated

This project is intended to demonstrate practical experience with:

- Linux system administration.
- IP addressing and network troubleshooting.
- DNS resolution and filtering.
- DHCP concepts and network configuration.
- Docker and Docker Compose.
- Container networking and persistent storage.
- Service logs and diagnostic workflows.
- Configuration management and Git.
- Technical documentation.
- Testing, verification, and infrastructure troubleshooting.

## References

- [Home Lab Repository](https://github.com/abhi9d01-png/Home-lab)
- [Pi-hole Documentation](https://docs.pi-hole.net/)
- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Documentation](https://docs.docker.com/compose/)
- [Dockge Repository](https://github.com/louislam/dockge)

---

## Project Status

The ThinkCentre Home Lab is an ongoing learning and infrastructure project. Its documentation records both successful tests and unresolved problems so that future changes can be made safely and reproduced.

The priority is to establish a stable host, reliable networking, a correctly configured Pi-hole deployment, and repeatable verification procedures before expanding the lab with additional services.
</WritingBlock>

**Before you commit:** make sure the paths referenced in this README match the files actually present in your repository. The configuration and test statuses above are documented as examples or pending checks where the final state has not been confirmed.**
