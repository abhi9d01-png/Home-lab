
# Pi-hole Troubleshooting Guide

This document records common problems, diagnostic commands, and recovery steps for the Pi-hole home-lab deployment.

**Project:** Home Lab  
**Service:** Pi-hole  
**Management:** Docker Compose and Dockge  
**Host:** Linux — ThinkCentre M91P  
**Last Updated:** 26 October 2026  
**Status:** In Progress

---

## Table of Contents

1. [Environment Details](#1-environment-details)
2. [Troubleshooting Principles](#2-troubleshooting-principles)
3. [Container Not Running](#3-container-not-running)
4. [Dashboard Not Accessible](#4-dashboard-not-accessible)
5. [DNS Resolution Problems](#5-dns-resolution-problems)
6. [Ads Not Being Blocked](#6-ads-not-being-blocked)
7. [Blocklist Download Failures](#7-blocklist-download-failures)
8. [Docker Networking Problems](#8-docker-networking-problems)
9. [DHCP and Wi-Fi Problems](#9-dhcp-and-wi-fi-problems)
10. [Admin Password Problems](#10-admin-password-problems)
11. [Persistent Configuration](#11-persistent-configuration)
12. [Verification Checklist](#12-verification-checklist)
13. [Incident Log](#13-incident-log)

---

## 1. Environment Details

The following values were recorded during the deployment. Verify them if the network changes.

| Component | Value |
|---|---|
| Linux host | ThinkCentre M91P |
| Router | TP-Link EX220-G2u v1 |
| Router IP | `192.168.0.1` |
| Pi-hole host IP | `192.168.0.119` |
| Network | `192.168.0.0/24` |
| Dashboard | `http://192.168.0.119/admin` |
| Container name | `pihole` |
| Management interface | Dockge |
| DNS port | `53` TCP/UDP |
| HTTP port | `80` |
| HTTPS port | `443` |

The current Docker network mode must be verified. The stack was changed from bridge networking to host networking during troubleshooting.

## 2. Troubleshooting Principles

Follow this order when a problem occurs:

1. Check whether the container is running.
2. Inspect the container logs.
3. Check the current network mode.
4. Test DNS directly against Pi-hole.
5. Check the Pi-hole Query Log.
6. Check router DHCP and client DNS settings.
7. Change one setting at a time.
8. Repeat the failed test and record the result.

Do not change router DHCP, Pi-hole DHCP, DNS settings, and Docker networking simultaneously. Making several changes at once makes it difficult to identify the cause of a problem.

### Initial diagnostic commands

Run these commands on the Linux host.

```bash
sudo docker ps --filter name=pihole
sudo docker logs --tail 60 pihole
sudo docker exec pihole pihole status
```

Expected result:

- The container appears as running.
- The logs do not show unresolved startup errors.
- Pi-hole reports its current blocking status.

A running container does not automatically mean that DNS resolution is working.

## 3. Container Not Running

### Symptoms

- The Pi-hole dashboard does not open.
- Docker does not list the container as running.
- Dockge reports a deployment or startup error.

### Diagnosis

Run:

```bash
sudo docker ps -a --filter name=pihole
sudo docker logs --tail 100 pihole
```

Check the output for:

- Invalid Compose configuration.
- Port conflicts.
- Permission errors.
- Missing configuration files.
- Startup or environment-variable errors.

### Recovery

1. Open the Pi-hole stack in Dockge.
2. Review the Compose configuration.
3. Check the logs for the exact error.
4. Correct the identified problem.
5. Redeploy the stack.
6. Repeat the container and DNS tests.

Do not delete the persistent Pi-hole directories to fix a startup error. They may contain important configuration data.

## 4. Dashboard Not Accessible

### Symptoms

The browser cannot open:

```text
http://192.168.0.119/admin
```

### Diagnosis

Check the container:

```bash
sudo docker ps --filter name=pihole
```

Check its network mode:

```bash
sudo docker inspect pihole \
  --format '{{.HostConfig.NetworkMode}}'
```

Check published ports:

```bash
sudo docker port pihole
```

The final command is useful for bridge networking. With host networking, Docker does not provide container port mappings in the same way.

### Recovery

1. Confirm that the Linux host is reachable.
2. Confirm that the container is running.
3. Verify the current Docker network mode.
4. Check whether another process uses the required web ports.
5. Review Pi-hole logs.
6. Try opening the dashboard again.

In host networking mode, do not add a `ports:` mapping to the Compose service.

Do not expose the administration interface directly to the public internet.

## 5. DNS Resolution Problems

### Symptoms

- Websites fail to load.
- DNS lookups time out.
- Pi-hole receives queries but does not return usable answers.
- Some domains resolve while others fail.

### Test DNS directly

Run:

```bash
nslookup google.com 192.168.0.119
```

This sends the DNS request directly to Pi-hole.

### Interpret the result

| Result | Meaning |
|---|---|
| A valid IP address is returned | Pi-hole answered the request |
| Request times out | The DNS service may be unreachable or unresponsive |
| Server failure | Investigate Pi-hole and upstream DNS |
| Network unreachable | Investigate routing and network connectivity |

A successful lookup does not prove that every device uses Pi-hole.

### Check the container

```bash
sudo docker exec pihole pihole status
sudo docker logs --tail 60 pihole
```

### Check upstream DNS

If Pi-hole receives the query but cannot resolve the domain, inspect the configured upstream DNS resolver and the Query Log.

Check the host's network connection before changing the upstream resolver.

### Recovery

1. Confirm the container is running.
2. Test DNS directly against `192.168.0.119`.
3. Inspect the Query Log.
4. Check the upstream resolver configuration.
5. Verify network connectivity.
6. Repeat the DNS lookup.

Avoid changing the router's DHCP or DNS settings until direct DNS resolution through Pi-hole is stable.

## 6. Ads Not Being Blocked

### Symptoms

- Advertisements remain visible.
- The dashboard shows permitted queries.
- A domain is not present in the blocklists.

### Check blocking status

```bash
sudo docker exec pihole pihole status
```

### Test a domain

```bash
nslookup doubleclick.net 192.168.0.119
```

Compare the result with the Pi-hole Query Log.

A normal public IP response may mean that the active rules do not block that domain. Check the actual query result before drawing a conclusion.

### Check the dashboard

Review:

- Query Log
- Blocked queries
- Permitted queries
- Enabled adlists
- Blocking status

### Important limitation

Pi-hole filters DNS requests. It cannot reliably block every advertisement.

Some advertisements, including many YouTube advertisements, are served from the same domains as normal content. Blocking those domains may also break legitimate services.

Do not add increasingly aggressive blocklists without checking their effects.

## 7. Blocklist Download Failures

### Symptoms

- An adlist fails to download.
- Gravity reports errors.
- A newly added list does not appear to affect blocking.

### Recorded issue

The following HaGeZi Multi NORMAL URL was attempted:

```text
https://raw.githubusercontent.com/hagezi/dns-blocklists/main/domains/multi.txt
```

The download failed during the recorded session.

An alternative URL was suggested:

```text
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/multi.txt
```

The alternative has not been confirmed as successful.

### Update gravity

Run:

```bash
sudo docker exec pihole pihole -g
```

Read the output and look for download errors.

### Recovery

1. Open the Pi-hole dashboard.
2. Check the configured adlist URL.
3. Confirm that the list is appropriate for Pi-hole.
4. Run the gravity update.
5. Read the full error message.
6. Verify that the list was downloaded and processed.
7. Test a relevant domain again.

Do not mark a blocklist as installed until the update succeeds.

If the update fails again, record the exact error and investigate the URL, network connectivity, and response from the hosting service.

## 8. Docker Networking Problems

### Check the network mode

```bash
sudo docker inspect pihole \
  --format '{{.HostConfig.NetworkMode}}'
```

### Check published ports

```bash
sudo docker port pihole
```

### Bridge networking

In bridge mode, the container uses a Docker-managed network. Required ports must be published to the host.

### Host networking

In host mode, the container shares the host's network namespace.

Important considerations:

- Do not configure Docker port mappings.
- Check for conflicts on ports 53, 80, and 443.
- Review host firewall rules.
- Verify that Pi-hole is listening on the expected interfaces.
- Restrict access to trusted devices.

### Recovery

1. Inspect the active Compose configuration in Dockge.
2. Confirm the network mode.
3. Check the logs.
4. Check whether another service uses the required ports.
5. Redeploy only after reviewing the configuration.
6. Test dashboard access and DNS resolution.

Do not assume that changing to host networking fixes every DNS problem.

## 9. DHCP and Wi-Fi Problems

### Recorded incident

During troubleshooting, router DHCP was reported disabled and Wi-Fi became unstable. Wi-Fi was later reported to be working, but the final DHCP settings were not independently verified.

### Safe temporary configuration

Until a deliberate DHCP migration is planned:

- Router DHCP: enabled.
- Pi-hole DHCP: disabled.

Do not run two DHCP servers on the same LAN unless a deliberate, tested design requires it.

### Recovery checklist

1. Check that router DHCP is enabled.
2. Check that Pi-hole DHCP is disabled.
3. Confirm that devices receive an IP address.
4. Confirm the default gateway is `192.168.0.1`.
5. Check which DNS server each client uses.
6. Test direct DNS resolution through Pi-hole.
7. Change one setting at a time.

The previously observed router DHCP pool was approximately:

```text
192.168.0.100 - 192.168.0.240
```

Review the actual router configuration before changing the pool.

### Network-wide DNS

Once Pi-hole DNS is stable, check whether the router supports advertising a custom DNS server through DHCP.

If the router does not support this, evaluate individual-device DNS settings or a carefully planned Pi-hole DHCP migration.

## 10. Admin Password Problems

### Symptoms

- The dashboard rejects the password.
- A password-setting command reports that an environment variable controls the password.
- The Compose configuration contains an empty password value.

### Diagnosis

Review the environment settings in Dockge.

The following setting was used during troubleshooting:

```yaml
FTLCONF_webserver_api_password: ''
```

Its final behavior and suitability were not verified.

### Recommended action

Use a strong password and avoid committing it to GitHub.

If the Compose configuration reads the password from a `.env` file, keep that file private and add it to `.gitignore`.

Example:

```dotenv
PIHOLE_WEB_PASSWORD=replace_with_a_strong_password
```

Replace the example with a strong, unique password.

If you change the password environment variable, redeploy the stack and verify dashboard login.

Do not publish the password in screenshots, logs, commits, or documentation.

## 11. Persistent Configuration

Pi-hole configuration directories used during setup:

```yaml
volumes:
  - './etc-pihole:/etc/pihole'
  - './etc-dnsmasq.d:/etc/dnsmasq.d'
```

These bind mounts store configuration on the host.

### Important precautions

- Do not delete these directories during routine troubleshooting.
- Keep backups before major configuration changes.
- Confirm that the Compose working directory is correct.
- Ensure that the directories remain available after a restart.
- Do not commit private credentials or sensitive configuration to GitHub.

A container restart should not normally require a complete Pi-hole reconfiguration when persistent storage is configured correctly.

## 12. Verification Checklist

Use this checklist after troubleshooting.

### Container

- [ ] Container is running.
- [ ] Logs show no unresolved startup errors.
- [ ] Current network mode is known.
- [ ] Pi-hole reports its blocking status.

### DNS

- [ ] `google.com` resolves through `192.168.0.119`.
- [ ] Upstream DNS resolution works consistently.
- [ ] Query Log shows the test request.
- [ ] A test domain's response matches the active blocking rules.

### Network

- [ ] Router DHCP is enabled.
- [ ] Pi-hole DHCP is disabled unless a migration is deliberately planned.
- [ ] Client devices receive valid network settings.
- [ ] Client DNS settings are known.
- [ ] Wi-Fi remains stable.

### Blocklists

- [ ] Existing blocklist is enabled.
- [ ] Additional list URLs are verified.
- [ ] Gravity completes without errors.
- [ ] Blocked and permitted queries are checked.

### Security

- [ ] Strong admin password is configured.
- [ ] Dashboard is restricted to the trusted LAN.
- [ ] Secrets are excluded from Git.
- [ ] Persistent configuration is backed up.

Do not mark a checklist item complete until it has been tested.

## 13. Incident Log

### 24 October 2026

**Observed:**

- Pi-hole dashboard became accessible.
- A DNS lookup for `google.com` succeeded.
- Queries appeared in the dashboard.
- Some domains appeared in blocked-domain statistics.
- The StevenBlack blocklist was enabled.

**Changes attempted:**

- Investigated router DHCP and Pi-hole DHCP.
- Changed Docker networking from bridge mode to host mode.
- Attempted to add the HaGeZi Multi NORMAL blocklist.
- Modified the web-password environment setting.

**Outstanding at the end of the session:**

- Verify the final Docker network mode.
- Confirm stable upstream DNS resolution.
- Verify the final DHCP arrangement.
- Confirm the HaGeZi blocklist download.
- Verify the final password configuration.

### Future incidents

For each new incident, record:

- Date and time
- Problem observed
- Command or action performed
- Exact output or error
- Root cause, if confirmed
- Fix applied
- Verification result

---

## Final Status

**Overall status:** In Progress

Pi-hole dashboard access and some DNS filtering have been demonstrated. Stable upstream DNS, final DHCP settings, successful additional blocklist updates, and network-wide client DNS configuration remain to be verified.
