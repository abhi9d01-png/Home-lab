# Pi-hole — Network-Wide DNS Filtering

**Project:** Home Lab  
**Status:** In Progress  
**Last Updated:** 24 October 2026  
**Platform:** Linux, Docker, Docker Compose, Dockge, Pi-hole

## 1. Project Overview

This project deploys Pi-hole in Docker to provide DNS-based ad blocking and network-wide DNS filtering for a home network.

The deployment runs on a Linux desktop computer. Docker Compose manages the container, and Dockge provides a web interface for managing the stack.

The main goals are:

- Deploy Pi-hole as a home-lab service.
- Filter DNS requests and block supported advertising and tracking domains.
- Monitor DNS requests through the Pi-hole dashboard.
- Maintain a stable home network.
- Document the deployment, configuration, testing, and troubleshooting process.
- Improve the setup through repeatable configuration and version-controlled documentation.

## 2. Hardware and Network Environment

| Component | Details |
|---|---|
| Host hardware | Lenovo ThinkCentre M91P |
| Operating system | Linux |
| Container runtime | Docker |
| Container management | Dockge |
| DNS filtering | Pi-hole |
| Router | TP-Link EX220-G2u v1 |
| Router/gateway IP | `192.168.0.1` |
| Pi-hole host IP | `192.168.0.119` |
| Pi-hole dashboard | `http://192.168.0.119/admin` |
| Network subnet | `192.168.0.0/24` |

The host IP and network details reflect the configuration observed during troubleshooting. Verify that these values remain correct before using them in another deployment.

## 3. Architecture

The intended network flow is:

```text
                  Internet
                      |
               TP-Link Router
                192.168.0.1
                      |
                 Home LAN
                      |
             Linux Home-Lab Host
                192.168.0.119
                      |
                 Docker Host
                      |
                   Pi-hole
                DNS filtering
                      |
             Upstream DNS Resolver
```

Client devices must use Pi-hole for DNS requests before Pi-hole can filter their DNS traffic.

**Important:** The diagram shows the intended DNS path. Network-wide DNS configuration has not yet been fully verified.

## 4. Current Deployment

Pi-hole has been deployed through a Docker Compose stack managed by Dockge.

The persistent volume mappings used during setup were:

```yaml
volumes:
  - ./etc-pihole:/etc/pihole
  - ./etc-dnsmasq.d:/etc/dnsmasq.d
```

These mappings preserve Pi-hole configuration data when the container is recreated.

During troubleshooting, the stack was changed to use host networking:

```yaml
network_mode: host
```

The previous configuration used Docker bridge networking. The current network mode and successful startup after the change must be verified on the host.

When using host networking, the container shares the host's network namespace. Docker port mappings are not used in this mode, and services can conflict with other processes using the same host ports.

Do not assume that a particular Compose configuration is the current working configuration without checking Dockge.

## 5. Completed and Verified Work

The following observations were recorded during the 24 October 2026 session:

- [x] Pi-hole dashboard became accessible at `http://192.168.0.119/admin`.
- [x] A DNS lookup for `google.com` succeeded when directed to `192.168.0.119`.
- [x] Client DNS queries appeared in the Pi-hole dashboard.
- [x] The dashboard showed blocked-domain statistics.
- [x] Some domains, including Mozilla telemetry and advertising-related domains, appeared in the blocked-domain statistics.
- [x] An existing StevenBlack hosts blocklist was enabled.
- [x] The Docker Compose configuration was modified to use host networking.

The last item records a configuration change, not proof that the final deployment is fully healthy.

## 6. Outstanding Work

The following items remain unverified or incomplete:

- [ ] Confirm that Pi-hole starts cleanly with the current Compose configuration.
- [ ] Confirm that upstream DNS resolution works consistently.
- [ ] Verify that the additional HaGeZi blocklist downloads successfully.
- [ ] Run a blocklist update and confirm that Pi-hole gravity completes without errors.
- [ ] Verify that router DHCP is enabled.
- [ ] Verify that Pi-hole DHCP is disabled until a deliberate DHCP migration is completed.
- [ ] Determine whether the router can advertise Pi-hole as the DNS server.
- [ ] Confirm that client devices actually use Pi-hole for DNS.
- [ ] Verify the web-password configuration and set a strong password.
- [ ] Document the final working Compose configuration.
- [ ] Test the setup after a host restart.

## 7. Network and DHCP Safety

During troubleshooting, the router's DHCP service was reported as disabled, and Wi-Fi became unstable. Wi-Fi was later reported to be working again, but the final DHCP settings were not independently confirmed.

Until a planned migration is completed:

1. Keep the TP-Link router's DHCP server enabled.
2. Keep Pi-hole's DHCP server disabled.
3. Avoid enabling both DHCP servers on the same LAN.
4. Verify that devices receive a valid IP address, gateway, and DNS configuration.
5. Do not change router-wide DNS settings until Pi-hole DNS resolution is stable.

The router's DHCP pool was previously shown as approximately `192.168.0.100` to `192.168.0.240`. Review the DHCP pool and address reservations before making further changes.

If the router supports custom DNS advertisement, configure Pi-hole as the DNS server after testing. If it does not, evaluate individual-device DNS configuration or a carefully planned Pi-hole DHCP setup.

## 8. Blocklist Configuration

The existing StevenBlack hosts list was enabled during the recorded session.

An additional HaGeZi Multi NORMAL blocklist was attempted using this URL:

```text
https://raw.githubusercontent.com/hagezi/dns-blocklists/main/domains/multi.txt
```

The download failed.

A second URL was suggested, but its success was not confirmed:

```text
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/multi.txt
```

Do not mark the HaGeZi list as installed until the URL has been verified, the list has been downloaded, and the gravity update has completed successfully.

### Blocklist update command

Run this on the Linux host:

```bash
sudo docker exec pihole pihole -g
```

Review the output for download errors and confirm that gravity completes successfully.

## 9. Troubleshooting and Verification

Run the following commands on the Linux host.

### Check the container

```bash
sudo docker ps --filter name=pihole
```

This checks whether the Pi-hole container is running.

### Check recent logs

```bash
sudo docker logs --tail 60 pihole
```

Look for startup errors, DNS errors, and configuration warnings.

### Check the network mode

```bash
sudo docker inspect pihole --format '{{.HostConfig.NetworkMode}}'
```

The output should match the intended deployment mode. The current mode was not conclusively verified after the Compose change.

### Check published ports

```bash
sudo docker port pihole
```

In bridge mode, check that the required ports are published. In host mode, Docker does not provide port mappings through this command.

### Check Pi-hole blocking status

```bash
sudo docker exec pihole pihole status
```

This reports Pi-hole's blocking status.

### Test DNS resolution through Pi-hole

```bash
nslookup google.com 192.168.0.119
```

A successful response confirms that Pi-hole answered that DNS request. It does not prove that all client devices use Pi-hole.

### Test an advertising-related domain

```bash
nslookup doubleclick.net 192.168.0.119
```

Compare the result with the Pi-hole Query Log. A blocking response depends on the active blocklists and Pi-hole configuration. A normal public IP response may indicate that the domain was not blocked by the active rules.

## 10. Security

The Pi-hole dashboard should remain accessible only from the trusted home network unless a separate, secure remote-access design is implemented.

Security requirements:

- Set a strong administrative password.
- Do not publish passwords or tokens in GitHub.
- Do not expose the Pi-hole dashboard directly to the public internet.
- Keep Docker, the operating system, and Pi-hole updated.
- Restrict DNS and administration access to the intended network.
- Keep persistent configuration and backups separate from public credentials.

An empty password environment setting was used during troubleshooting. Its final behavior and suitability have not been verified. Replace it with a strong password before treating the deployment as production-ready.

## 11. Known Limitations

Pi-hole uses DNS filtering. It cannot reliably block every advertisement.

Some advertisements, including many YouTube advertisements, may be served from the same domains used for normal content. DNS filtering can also block a domain required by a legitimate service.

If a website or application stops working after a blocklist update, review the Query Log before changing the configuration.

## 12. Project Roadmap

### Phase 1 — Deployment

- [x] Deploy Pi-hole through Docker and Dockge.
- [x] Make the dashboard accessible.
- [x] Observe DNS queries and some blocked domains.
- [ ] Verify the final Compose configuration.
- [ ] Confirm clean startup and persistent configuration.

### Phase 2 — DNS Reliability

- [ ] Confirm stable upstream DNS resolution.
- [ ] Test DNS requests directly against Pi-hole.
- [ ] Confirm the router's DHCP configuration.
- [ ] Configure client DNS safely.
- [ ] Verify DNS filtering on multiple client devices.

### Phase 3 — Blocklists

- [x] Enable the existing StevenBlack list.
- [ ] Resolve the HaGeZi download failure.
- [ ] Complete a successful gravity update.
- [ ] Verify blocked and permitted queries.

### Phase 4 — Security and Maintenance

- [ ] Set a strong administrative password.
- [ ] Document the verified Compose configuration.
- [ ] Back up persistent Pi-hole data.
- [ ] Test recovery after a container restart.
- [ ] Document update and maintenance procedures.

## 13. Next Actions

The next session should focus on network stability and DNS reliability before adding more blocklists.

Recommended order:

1. Confirm that router DHCP is enabled and Pi-hole DHCP is disabled.
2. Check the Pi-hole container state and recent logs.
3. Verify the current Docker network mode.
4. Test DNS resolution through Pi-hole.
5. Investigate the exact HaGeZi download error.
6. Confirm that client devices use Pi-hole for DNS.
7. Update this README with verified results.

## 14. Change Log

### 24 October 2026

- Recorded the Linux host and home-network configuration.
- Verified dashboard access.
- Observed successful DNS resolution in an earlier test.
- Observed DNS queries and blocked-domain statistics.
- Investigated the router DHCP and Pi-hole DHCP configuration.
- Changed the Compose configuration to use host networking.
- Attempted to add the HaGeZi Multi NORMAL blocklist.
- Recorded outstanding DNS, DHCP, password, and blocklist verification tasks.

---

**Project status:** In Progress

**Current milestone:** Pi-hole dashboard access and some DNS filtering have been demonstrated. Stable upstream DNS, the final DHCP arrangement, and network-wide client configuration still require verification.
