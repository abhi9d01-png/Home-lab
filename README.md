
# Home Lab

A personal home lab for learning, testing, and documenting self-hosted services, Docker deployments, networking, DNS, and system administration.

This repository records the setup process, configuration decisions, troubleshooting steps, test results, and future improvements for my home lab. The goal is to build practical infrastructure skills and maintain a clear technical portfolio.

**Repository:** [Home-lab](https://github.com/abhi9d01-png/Home-lab)

---

## Table of Contents

- [Overview](#overview)
- [Goals](#goals)
- [Hardware and Environment](#hardware-and-environment)
- [Network Overview](#network-overview)
- [Projects](#projects)
- [Pi-hole DNS Filtering](#pi-hole-dns-filtering)
- [Architecture](#architecture)
- [Repository Structure](#repository-structure)
- [Security and Privacy](#security-and-privacy)
- [Testing and Verification](#testing-and-verification)
- [Known Issues and Pending Work](#known-issues-and-pending-work)
- [Troubleshooting](#troubleshooting)
- [Future Improvements](#future-improvements)
- [Learning Outcomes](#learning-outcomes)
- [License](#license)

---

## Overview

This home lab uses a physical computer running Linux to experiment with self-hosted applications and network services.

The first documented service is **Pi-hole**, a DNS-based network ad and tracker blocker deployed using Docker and managed through Dockge.

The project includes:

- Self-hosted DNS filtering.
- Docker container deployment and management.
- Persistent application configuration.
- Local network troubleshooting.
- DNS query testing and verification.
- Documentation of failures, fixes, and outstanding tasks.

This is an ongoing learning project. Features and tests are documented according to their observed status rather than being assumed to work.

## Goals

1. Learn how to deploy and maintain services on Linux.
2. Understand Docker containers, volumes, networking, and persistent storage.
3. Run a DNS filtering service on the local network.
4. Understand how clients resolve domain names through a DNS server.
5. Learn to diagnose connectivity, DNS, and container issues.
6. Document configurations and test results in a reproducible way.
7. Build a practical portfolio project demonstrating infrastructure and networking skills.

## Hardware and Environment

| Component | Details |
|---|---|
| Host computer | Lenovo ThinkCentre M91P |
| Host operating system | Linux |
| Container platform | Docker |
| Container management | Dockge |
| DNS filtering software | Pi-hole |
| Router | TP-Link EX220-G2u v1 |
| Primary project | Local DNS filtering and network troubleshooting |
| Time zone | Asia/Kolkata |

The exact Linux distribution, operating system version, Docker version, and Dockge version should be recorded when verified.

## Network Overview

The following addresses were observed during setup. They may change if the router's DHCP configuration or host network configuration changes.

| Device or service | Observed address |
|---|---|
| Router / default gateway | `192.168.0.1` |
| Linux home lab host | `192.168.0.119` |
| Pi-hole administration page | `http://192.168.0.119/admin` |
| Observed router DHCP pool | Approximately `192.168.0.100`–`192.168.0.240` |

**Important:** The host address should remain stable for reliable DNS service. Use a router DHCP reservation or an appropriately configured static address, ensuring the address does not conflict with another device.

### Intended DNS flow

```text
Device on the local network
          |
          | DNS query
          v
      Pi-hole
   192.168.0.119
          |
          | Allowed domain queries
          v
   Upstream DNS resolver
          |
          v
     DNS response
          |
          v
    Client device
```

Domains covered by active blocking rules can be blocked by Pi-hole instead of being resolved normally.

This flow represents the intended design. Network-wide use must be verified on individual client devices.

## Projects

| Project | Description | Documentation |
|---|---|---|
| Pi-hole | Self-hosted DNS filtering using Docker | [`projects/04-pi-hole/`](projects/04-pi-hole/README.md) |
| Home lab documentation | Architecture, configuration, tests, and troubleshooting | [`doc/`](doc/) |
| Network diagrams | Visual representation of the lab and its services | [`diagrams/`](diagrams/) |
| Scripts | Helper scripts and automation, where available | [`scripts/`](scripts/) |

Additional projects will be added as they are built and tested.

---

## Pi-hole DNS Filtering

### Purpose

Pi-hole provides DNS-based filtering for advertisements, trackers, and other domains included in configured blocklists.

It can be used as a DNS server for individual devices or, after appropriate router configuration and testing, for devices across the local network.

### Deployment approach

Pi-hole is deployed as a Docker container and managed through Dockge.

The configuration has used persistent directories:

```text
projects/04-pi-hole/
├── docker-compose.yml
├── .env
├── .gitignore
├── README.md
├── troubleshooting.md
├── test-results.md
├── etc-pihole/
└── etc-dnsmasq.d/
```

The exact files present on the host may differ from this intended layout. Runtime data directories should not be committed to GitHub.

### Container networking

Two Docker networking approaches were considered during troubleshooting:

- **Docker bridge networking:** The container uses an isolated Docker network and requires suitable port mappings for services that must be reachable from the LAN.
- **Host networking:** The container uses the host's network namespace. Separate Docker `ports:` mappings are not required, but DNS and web-service ports can conflict with services already running on the host.

The configuration was later changed to host networking during troubleshooting. The final running configuration must be checked before assuming that the deployment is stable.

### Persistent storage

The intended persistent mounts are:

```yaml
volumes:
  - "./etc-pihole:/etc/pihole"
  - "./etc-dnsmasq.d:/etc/dnsmasq.d"
```

These directories preserve Pi-hole configuration and DNS-related settings across container recreation.

Back up important configuration before making significant changes.

### Configuration management

The deployment uses the following configuration concepts:

- Time zone: `Asia/Kolkata`
- DNS listening mode: configured for the intended network access
- Web interface password: should be set to a strong, private value
- Restart policy: `unless-stopped`
- Persistent volumes: enabled in the intended configuration

The actual environment variables and container settings must match the current Compose file. Do not publish passwords, private configuration, or secrets in this repository.

### Accessing the dashboard

When the host and container are running correctly, open:

**[Pi-hole Dashboard](http://192.168.0.119/admin)**

This address is valid only while the host retains the recorded IP address and the dashboard is reachable on the local network.

### Blocklists

The setup initially used the StevenBlack list.

An additional HaGeZi blocklist was attempted, but the first URL did not download successfully:

```text
https://raw.githubusercontent.com/hagezi/dns-blocklists/main/domains/multi.txt
```

An alternative URL was suggested during troubleshooting:

```text
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/multi.txt
```

The alternative has not been confirmed as successfully imported. Verify that a list is downloaded, accepted by Pi-hole, and included in a successful gravity update before marking it active.

More blocklists do not automatically mean better results. Use compatible lists and check for false positives.

### DHCP considerations

The router's DHCP service and Pi-hole's DHCP service must not both assign addresses on the same LAN unless a deliberately designed DHCP arrangement supports it.

During troubleshooting, the router DHCP service was reported as disabled, and Pi-hole DHCP was configured with a proposed range of `192.168.0.150`–`192.168.0.240`. The final state was not conclusively verified.

**Safe operating rule:** Keep the router's DHCP enabled and Pi-hole DHCP disabled until the Pi-hole DHCP setup has been deliberately tested and there is a clear migration plan. Before enabling Pi-hole DHCP, confirm that the router DHCP server is disabled and that clients can still obtain valid addresses.

See [`projects/04-pi-hole/troubleshooting.md`](projects/04-pi-hole/troubleshooting.md) for diagnostic guidance.

---

## Architecture

```text
                   Internet
                      |
                      v
                Home Router
                 192.168.0.1
                      |
              Local Network / LAN
                      |
                      v
          Linux ThinkCentre M91P
                192.168.0.119
                      |
                      v
                  Docker
                      |
                      v
                  Pi-hole
              DNS + Web UI
                      |
                      v
              Upstream DNS
```

### Main components

| Component | Responsibility |
|---|---|
| Router | Network connectivity, gateway, and normally DHCP |
| Linux host | Runs the self-hosted services |
| Docker | Runs Pi-hole in a container |
| Dockge | Provides a graphical interface for managing Docker Compose stacks |
| Pi-hole | DNS filtering, query logging, and administration |
| Persistent volumes | Preserve service configuration |
| Upstream DNS resolver | Resolves allowed external domain queries |

The diagram shows the intended logical architecture. Actual routing, port availability, DHCP settings, and upstream resolver health still need verification.

## Repository Structure

The repository uses `doc/` for general documentation. Keep this folder name consistent with the links in the README.

```text
Home-lab/
├── README.md
├── doc/
│   └── General home lab documentation
├── projects/
│   └── 04-pi-hole/
│       ├── README.md
│       ├── docker-compose.yml
│       ├── .env
│       ├── .gitignore
│       ├── troubleshooting.md
│       └── test-results.md
├── diagrams/
│   └── Network and architecture diagrams
└── scripts/
    └── Helper scripts and automation
```

This is a reference structure, not a guarantee that every listed file or directory already exists. Add links as documentation is created and verify paths before committing.

## Security and Privacy

Security is part of the home lab design.

- Never commit `.env` files containing real passwords or tokens.
- Keep application data directories and private backups out of Git.
- Use a strong password for the Pi-hole administration interface.
- Do not expose the Pi-hole dashboard or DNS service directly to the public internet.
- Restrict administrative access to trusted devices on the local network.
- Keep Docker images and the host operating system updated.
- Back up persistent configuration before changing networking or DHCP.
- Avoid publishing personal network information beyond what is necessary for the project.
- Check `.gitignore` before committing configuration or runtime files.

A suitable `.gitignore` for the Pi-hole project should exclude at least:

```gitignore
.env
etc-pihole/
etc-dnsmasq.d/
*.log
```

Only exclude directories that contain runtime data; retain the safe, sanitized Compose configuration and project documentation in Git.

## Testing and Verification

The setup has been tested in stages, but some tests still require a fresh run.

| Test | Recorded status | Notes |
|---|---|---|
| Dashboard access | Previously passed | Dashboard was accessible during setup |
| DNS lookup through Pi-hole | Previously passed | `google.com` resolved using the Pi-hole address |
| Query logging | Observed | Queries appeared in the dashboard |
| Domain blocking | Partial | Some blocked-domain activity was observed; comprehensive verification is pending |
| Container after networking change | Retest required | Confirm the current host-network configuration starts cleanly |
| Upstream DNS reliability | Retest required | Some lookups failed during troubleshooting |
| HaGeZi blocklist download | Failed initially | The first attempted URL failed |
| Gravity update | Unverified | Confirm the update completes successfully |
| Network-wide client DNS | Unverified | Test from a client using Pi-hole as its DNS server |
| Final DHCP arrangement | Unverified | Confirm exactly one intended DHCP server is active |
| Admin password | Retest required | Verify that a strong password is configured |
| Persistence after restart | Unverified | Restart the container and check settings and lists |

The detailed checklist belongs in [`projects/04-pi-hole/test-results.md`](projects/04-pi-hole/test-results.md).

### Basic verification commands

Run these commands on the Linux host.

Check whether the container is running:

```bash
sudo docker ps --filter name=pihole
```

Inspect recent logs:

```bash
sudo docker logs --tail 60 pihole
```

Check Pi-hole status:

```bash
sudo docker exec pihole pihole status
```

Check the active Docker network mode:

```bash
sudo docker inspect pihole --format '{{.HostConfig.NetworkMode}}'
```

Test DNS resolution through Pi-hole:

```bash
nslookup google.com 192.168.0.119
```

Test a domain expected to be blocked by an active list:

```bash
nslookup doubleclick.net 192.168.0.119
```

A failed lookup alone does not prove that blocking works. Upstream DNS failures, routing problems, or a blocked resolver can produce similar results. Check the Pi-hole query log and container logs to identify the actual cause.

---

## Known Issues and Pending Work

The following items remain open until verified on the running system:

1. Confirm that the Pi-hole container starts reliably using its current network configuration.
2. Confirm that DNS queries resolve consistently through the intended upstream resolver.
3. Investigate the DNS failures observed during troubleshooting.
4. Verify that the additional blocklist downloads and imports successfully.
5. Run a gravity update and inspect its result.
6. Test blocking against a domain included in an active blocklist.
7. Confirm the router and Pi-hole DHCP settings before changing either service.
8. Verify DNS settings on a separate client device.
9. Confirm the web interface uses a strong password.
10. Restart the container and confirm configuration persistence.
11. Check that the repository excludes private credentials and runtime data.
12. Confirm all documentation links and directory names match the actual repository.

Do not mark these tasks complete until the relevant test passes.

## Troubleshooting

For detailed steps and expected results, use:

- [Pi-hole troubleshooting guide](projects/04-pi-hole/troubleshooting.md)
- [Pi-hole test results and verification checklist](projects/04-pi-hole/test-results.md)
- [Pi-hole project README](projects/04-pi-hole/README.md)

Common areas to investigate include:

- Container startup and logs.
- Port conflicts on DNS ports 53 TCP/UDP.
- Dashboard accessibility.
- Host firewall and LAN connectivity.
- Upstream DNS resolution.
- Blocklist download and gravity update failures.
- DHCP conflicts and client address assignment.
- Persistent volume permissions and configuration.

Change one variable at a time and retest so the cause of each issue remains clear.

## Future Improvements

Planned improvements for the home lab include:

- Complete and document the Pi-hole verification checklist.
- Create a network topology diagram.
- Document the Linux host's operating system and hardware details.
- Add backup and recovery instructions.
- Add lightweight service monitoring.
- Record resource usage and container health.
- Explore additional self-hosted services as the foundation becomes stable.
- Add sanitized configuration examples and reproducible setup instructions.
- Keep a dated changelog of significant infrastructure changes.

## Learning Outcomes

This project is intended to demonstrate practical experience with:

- Linux system administration.
- Docker and Docker Compose.
- Self-hosted application deployment.
- DNS resolution and filtering.
- Basic LAN addressing and DHCP.
- Network troubleshooting.
- Persistent storage and configuration management.
- Security-conscious handling of secrets.
- Technical documentation and verification.

## License

No license has been specified yet. Add a `LICENSE` file if you decide to make the repository available for reuse, and select a license that matches your intentions.
