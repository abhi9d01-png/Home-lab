
# Pi-hole Test Results

**Project:** Home Lab  
**Service:** Pi-hole DNS Filtering  
**Test Environment:** Linux, Docker, Dockge  
**Host:** ThinkCentre M91P  
**Last Updated:** 26 October 2026  
**Overall Status:** In Progress

---

## 1. Purpose

This document records the test results for the Pi-hole home-lab deployment.

The objectives are to verify:

- Container availability and startup.
- Dashboard accessibility.
- DNS resolution.
- DNS query logging.
- Domain blocking.
- Blocklist updates.
- Docker networking.
- DHCP and client DNS configuration.
- Administrative access and persistent configuration.

Only tests supported by observed results should be marked as passed.

## 2. Test Environment

| Component | Recorded Value |
|---|---|
| Host | Lenovo ThinkCentre M91P |
| Operating system | Linux |
| Container runtime | Docker |
| Stack management | Dockge |
| DNS filtering service | Pi-hole |
| Router | TP-Link EX220-G2u v1 |
| Router IP | `192.168.0.1` |
| Pi-hole host IP | `192.168.0.119` |
| Dashboard URL | `http://192.168.0.119/admin` |
| Network subnet | `192.168.0.0/24` |

These values were recorded during the deployment. Verify them if the network configuration changes.

## 3. Test Status Definitions

| Status | Meaning |
|---|---|
| PASS | The test completed successfully and the result was observed. |
| FAIL | The test was performed and did not meet the expected result. |
| PARTIAL | Some functionality was demonstrated, but full verification is incomplete. |
| NOT TESTED | No confirmed result is available. |
| NEEDS RETEST | A previous result exists, but the current configuration needs verification. |

## 4. Test Summary

| Test ID | Test | Status |
|---|---|---|
| T01 | Dashboard accessibility | PASS — previously observed |
| T02 | DNS lookup through Pi-hole | PASS — earlier test |
| T03 | DNS query logging | PASS — previously observed |
| T04 | Domain blocking | PARTIAL |
| T05 | Container startup after host networking change | NEEDS RETEST |
| T06 | Upstream DNS reliability | NEEDS RETEST |
| T07 | HaGeZi blocklist download | FAIL — initial attempt |
| T08 | HaGeZi gravity update | NOT TESTED |
| T09 | Final router DHCP configuration | NEEDS RETEST |
| T10 | Network-wide client DNS | NOT TESTED |
| T11 | Admin password security | NEEDS RETEST |
| T12 | Configuration persistence after restart | NOT TESTED |

The statuses above reflect the observations recorded during the 24 October 2026 session. They do not establish the current health of the deployment.

---

## 5. Detailed Test Cases

### T01 — Dashboard Accessibility

**Objective:** Confirm that the Pi-hole administration dashboard is reachable.

**Test URL:**

```text
http://192.168.0.119/admin
```

**Expected result:** The Pi-hole dashboard loads successfully.

**Recorded result:** The dashboard was accessible during the troubleshooting session.

**Status:** PASS — previously observed.

**Next action:** Open the dashboard again to confirm current accessibility.

---

### T02 — DNS Resolution Through Pi-hole

**Objective:** Confirm that Pi-hole can answer a DNS request.

**Command:**

```bash
nslookup google.com 192.168.0.119
```

**Expected result:** The DNS request receives a valid response.

**Recorded result:** A successful DNS answer was observed during an earlier test.

**Status:** PASS — earlier test.

**Limitation:** This test does not prove that upstream DNS is consistently available or that all client devices use Pi-hole.

**Next action:** Repeat the test and record the current output.

---

### T03 — DNS Query Logging

**Objective:** Confirm that DNS requests appear in Pi-hole's query statistics.

**Procedure:**

1. Open the Pi-hole dashboard.
2. Open the Query Log.
3. Generate a DNS request from a client.
4. Check whether the request appears.

**Expected result:** The query appears in the log.

**Recorded result:** Client DNS queries and dashboard statistics were observed.

**Status:** PASS — previously observed.

**Next action:** Repeat the test after confirming the current network configuration.

---

### T04 — Domain Blocking

**Objective:** Confirm that active blocklists can block a DNS request.

**Test command:**

```bash
nslookup doubleclick.net 192.168.0.119
```

**Expected result:** If the domain is blocked by the active rules, Pi-hole returns the configured blocking response.

**Recorded result:** The dashboard showed blocked-domain statistics. Examples included:

- `incoming.telemetry.mozilla.org`
- `ads-img.mozilla.org`
- `ads.mozilla.org`

**Status:** PARTIAL.

**Reason:** Some blocking was observed, but a controlled test with a recorded blocking response was not fully documented.

**Next action:**

1. Run the test command.
2. Inspect the corresponding Query Log entry.
3. Record whether the query was blocked or permitted.
4. Record the response and the rule responsible, if available.

A successful DNS lookup alone does not establish whether a domain was blocked.

---

### T05 — Container Startup and Networking

**Objective:** Confirm that Pi-hole starts correctly with its current Docker network configuration.

**Commands:**

```bash
sudo docker ps --filter name=pihole
sudo docker inspect pihole --format '{{.HostConfig.NetworkMode}}'
sudo docker logs --tail 60 pihole
```

**Expected result:**

- The container is running.
- The network mode matches the intended configuration.
- No unresolved startup errors are present.

**Recorded result:** The stack was changed from bridge networking to host networking. Dashboard access was subsequently observed, but final startup health was not conclusively verified.

**Status:** NEEDS RETEST.

**Next action:** Run the commands and record the actual output.

---

### T06 — Upstream DNS Reliability

**Objective:** Confirm that Pi-hole can consistently resolve permitted domains.

**Command:**

```bash
nslookup google.com 192.168.0.119
```

**Expected result:** Repeated DNS requests complete successfully.

**Recorded result:** An earlier lookup succeeded. A later connectivity test encountered network-unreachable or resolver errors.

**Status:** NEEDS RETEST.

**Next action:**

1. Check the container logs.
2. Check the configured upstream resolver.
3. Repeat the DNS test.
4. Compare the results with the Query Log.
5. Record any intermittent failures.

Do not mark upstream DNS as stable until repeated tests succeed.

---

### T07 — HaGeZi Blocklist Download

**Objective:** Add the HaGeZi Multi NORMAL blocklist.

**Initial URL:**

```text
https://raw.githubusercontent.com/hagezi/dns-blocklists/main/domains/multi.txt
```

**Expected result:** Pi-hole downloads and processes the list successfully.

**Recorded result:** The initial download attempt failed.

**Status:** FAIL — initial attempt.

**Alternative URL suggested for testing:**

```text
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/multi.txt
```

**Next action:**

1. Verify the URL and the list format.
2. Add the list through the Pi-hole dashboard.
3. Run a gravity update.
4. Inspect the update output.
5. Confirm that the list was downloaded and processed.

The alternative URL has not been confirmed as working.

---

### T08 — Gravity Update

**Objective:** Confirm that Pi-hole can update its blocking database.

**Command:**

```bash
sudo docker exec pihole pihole -g
```

**Expected result:** The update completes without unresolved download or processing errors.

**Recorded result:** The command was suggested during troubleshooting, but a successful final run was not documented.

**Status:** NOT TESTED.

**Next action:** Run the command and preserve the output in the project notes.

---

### T09 — DHCP Configuration

**Objective:** Confirm that DHCP is configured safely.

**Expected temporary configuration:**

- Router DHCP: enabled.
- Pi-hole DHCP: disabled.

**Recorded result:** Router DHCP was reported disabled during troubleshooting. Wi-Fi later recovered, but the final DHCP settings were not independently verified.

**Status:** NEEDS RETEST.

**Next action:**

1. Confirm router DHCP is enabled.
2. Confirm Pi-hole DHCP is disabled.
3. Check that client devices receive valid IP addresses.
4. Confirm the default gateway is `192.168.0.1`.
5. Verify client DNS settings.

Do not run both DHCP servers on the same LAN unless a deliberate, tested design requires it.

---

### T10 — Network-Wide Client DNS

**Objective:** Confirm that home devices use Pi-hole for DNS.

**Expected result:** Client DNS requests pass through Pi-hole and appear in the Query Log.

**Recorded result:** Queries were visible in the dashboard, but the final DNS configuration for every home device was not confirmed.

**Status:** NOT TESTED.

**Next action:**

1. Select a test client.
2. Inspect its DNS server configuration.
3. Generate a DNS request.
4. Check the Query Log.
5. Repeat on other devices.

Do not claim network-wide filtering until the relevant clients have been tested.

---

### T11 — Admin Password Security

**Objective:** Confirm that dashboard access uses a strong password.

**Recorded result:** An empty password environment setting was used during troubleshooting. The final password configuration was not verified.

**Status:** NEEDS RETEST.

**Next action:**

1. Configure a strong, unique administrative password.
2. Verify that dashboard authentication works.
3. Confirm that credentials are not committed to GitHub.
4. Keep the administration interface restricted to the trusted LAN.

Never store real passwords in this test-results document.

---

### T12 — Configuration Persistence

**Objective:** Confirm that Pi-hole configuration persists across container recreation or restart.

**Expected configuration directories:**

```text
./etc-pihole
./etc-dnsmasq.d
```

**Expected result:** Existing settings remain available after a controlled restart.

**Recorded result:** Persistent volume mappings were used during setup, but a complete persistence test was not documented.

**Status:** NOT TESTED.

**Next action:**

1. Back up the configuration.
2. Confirm the actual volume mappings.
3. Perform a controlled container restart.
4. Verify that settings and adlists remain available.
5. Repeat DNS and dashboard tests.

Do not delete persistent directories to perform this test.

---

## 6. Recommended Test Sequence

Run the tests in this order:

```text
Check container
      |
      v
Check logs and network mode
      |
      v
Open the dashboard
      |
      v
Test DNS resolution
      |
      v
Inspect the Query Log
      |
      v
Verify blocking behavior
      |
      v
Check blocklist updates
      |
      v
Verify DHCP and client DNS
      |
      v
Verify security and persistence
```

This sequence checks basic service health before making network-wide changes.

## 7. Test Execution Record

Use this table to record future test runs.

| Date | Test ID | Result | Evidence or Notes |
|---|---|---|---|
| 24 Oct 2026 | T01 | Pass — previously observed | Dashboard accessible |
| 24 Oct 2026 | T02 | Pass — earlier test | DNS answer received |
| 24 Oct 2026 | T03 | Pass — previously observed | Queries appeared in dashboard |
| 24 Oct 2026 | T04 | Partial | Blocked-domain statistics observed |
| 24 Oct 2026 | T07 | Fail — initial attempt | HaGeZi download failed |
| Pending | T05 | Not verified | Check current container and network mode |
| Pending | T06 | Not verified | Retest upstream DNS |
| Pending | T08 | Not verified | Run gravity update |
| Pending | T09 | Not verified | Confirm DHCP settings |
| Pending | T10 | Not verified | Test client DNS |
| Pending | T11 | Not verified | Verify strong admin password |
| Pending | T12 | Not verified | Test persistent configuration |

Update this table after each test session. Do not replace historical results; add a new row when a test is repeated.

## 8. Completion Criteria

The Pi-hole deployment can be considered operationally verified when:

- [ ] The container starts without unresolved errors.
- [ ] The dashboard is accessible on the trusted LAN.
- [ ] Permitted DNS requests resolve consistently.
- [ ] The Query Log records client requests.
- [ ] A controlled test confirms blocking behavior.
- [ ] Gravity updates complete successfully.
- [ ] Router DHCP and Pi-hole DHCP are configured safely.
- [ ] Intended client devices use Pi-hole for DNS.
- [ ] A strong admin password is configured.
- [ ] Persistent configuration survives a controlled restart.

## 9. Final Status

**Overall status:** In Progress.

Dashboard access, DNS query logging, and some domain blocking were observed during the 24 October 2026 session. The final network configuration, upstream DNS reliability, additional blocklist installation, client DNS settings, and configuration persistence still require verification.
