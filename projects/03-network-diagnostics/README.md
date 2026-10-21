
# 03 — Home Network Diagnostics

**Project:** ThinkCentre Home Lab  
**Platform:** Lenovo ThinkCentre M91p  
**Operating System:** Linux Mint  
**Focus:** TCP/IP, IP addressing, DNS, routing, connectivity, and troubleshooting

## 1. Overview

This project explores the fundamentals of computer networking using my Linux home lab and existing home router.

The goal is to understand how devices communicate across a local network, how Linux manages network interfaces and routes, and how to diagnose common connectivity problems using command-line tools.

The project emphasizes practical testing, network documentation, and reproducible troubleshooting procedures.

## 2. Objectives

- Understand IPv4 addressing and subnet masks.
- Identify network interfaces and assigned IP addresses.
- Understand default gateways and routing tables.
- Test IP connectivity and DNS resolution.
- Investigate common network failures.
- Document the home network topology.
- Understand Docker networking and its relationship to the host network.
- Build a repeatable network troubleshooting workflow.

## 3. Environment

| Component | Details |
|---|---|
| Physical Server | Lenovo ThinkCentre M91p |
| Operating System | Linux Mint |
| Network Equipment | Existing home router |
| Network Interface | To be verified |
| Address Assignment | DHCP or static; to be verified |
| Diagnostic Tools | iproute2, ping, getent |
| Container Networking | Docker, if applicable |

Actual network details will be recorded after inspecting the system.

## 4. Network Topology

The expected high-level architecture is:

```text
              Internet
                  |
             Home Router
                  |
             Home LAN
                  |
        Lenovo ThinkCentre M91p
                  |
              Linux Mint
                  |
          Docker Containers
          (when applicable)
```

Other devices may connect to the same router through Ethernet or Wi-Fi.

The final topology diagram should reflect the actual connections and services present in the lab.

## 5. Network Interface Inspection

List network interfaces and their addresses:

```bash
ip -br addr
```

Inspect interface details and link state:

```bash
ip link show
```

Inspect the routing table:

```bash
ip route
```

### What to investigate

- Which interface connects the ThinkCentre to the network?
- Is the interface active?
- Does it have an IPv4 address?
- Which route is used to reach external networks?
- Is the address assigned dynamically or configured statically?

Document the findings in a network inventory.

## 6. IP Addressing and Subnetting

Record the following information after verification:

| Property | Value |
|---|---|
| Host IP Address | To be verified |
| Subnet Prefix | To be verified |
| Network Address | To be calculated |
| Default Gateway | To be verified |
| DNS Resolver | To be verified |
| Address Assignment | To be verified |

Learn to distinguish between:

- IP addresses assigned to individual devices.
- Network addresses identifying subnets.
- Broadcast addresses in IPv4 subnets.
- Private IPv4 address ranges.
- Default gateways used to reach other networks.

Do not assume the home router uses a particular subnet. Inspect the actual configuration.

## 7. Connectivity Testing

### Test 1: Local Network Connectivity

After identifying the actual gateway:

```bash
ping -c 4 GATEWAY_IP
```

Replace `GATEWAY_IP` with the verified gateway address.

Record whether replies were received, along with packet loss and latency.

Some routers may block ICMP traffic, so a failed ping does not automatically prove that the gateway is unavailable.

### Test 2: External IP Connectivity

```bash
ping -c 4 1.1.1.1
```

This tests ICMP connectivity to an external IP address, provided the destination is reachable and ICMP is permitted.

### Test 3: DNS Resolution

```bash
getent hosts github.com
```

Inspect resolver configuration:

```bash
cat /etc/resolv.conf
```

Compare IP connectivity and hostname resolution to help identify DNS-related problems.

### Test Results

| Test | Expected Result | Actual Result |
|---|---|---|
| Gateway connectivity | Replies, if ICMP permitted | Pending |
| External IP connectivity | Replies, if reachable | Pending |
| DNS resolution | Hostname resolves | Pending |
| Default route | Correct route present | Pending |

Only replace pending results with observations from real tests.

## 8. DNS Troubleshooting

When a hostname cannot be resolved:

1. Check the network interface and assigned address.
2. Inspect the routing table.
3. Test connectivity to the gateway.
4. Test external IP connectivity.
5. Inspect DNS resolver configuration.
6. Retry hostname resolution.
7. Record the evidence and identify the likely failure point.

A failed DNS lookup may result from resolver configuration, network connectivity, filtering, or a remote DNS service problem.

## 9. Routing and Packet Paths

Inspect the route used to reach a destination:

```bash
ip route get 1.1.1.1
```

Inspect the routing table:

```bash
ip route
```

Document:

- The default route.
- The outgoing interface.
- The selected next hop, if applicable.
- How the host distinguishes local traffic from traffic for remote networks.

Advanced route changes should be tested carefully because incorrect settings can disconnect the server.

## 10. Docker Networking

Docker may create virtual networks that allow containers to communicate with one another.

Inspect Docker networks:

```bash
docker network ls
```

Inspect a specific network:

```bash
docker network inspect NETWORK_NAME
```

Inspect container port mappings:

```bash
docker ps
docker port CONTAINER_NAME
```

Document:

- Docker network drivers in use.
- Container IP addressing where relevant.
- Published host ports.
- Communication between containers.
- The distinction between host networking and container networking.

Do not assume all containers are connected to the same network or that every container port is accessible from the LAN.

## 11. Troubleshooting Workflow

Use this sequence when diagnosing a connectivity issue:

1. Confirm the physical or wireless connection.
2. Check interface state and IP configuration.
3. Inspect the routing table.
4. Test gateway connectivity.
5. Test external IP connectivity.
6. Test DNS resolution.
7. Inspect relevant firewall rules and service logs.
8. Document the root cause, if confirmed.
9. Repeat the test to verify the resolution.

### Troubleshooting Log

| Date | Symptom | Diagnosis | Resolution | Status |
|---|---|---|---|---|
| To be recorded | To be recorded | To be verified | To be documented | Pending |

## 12. Security and Privacy

- Do not publish Wi-Fi passwords or router credentials.
- Sanitize public IP addresses and sensitive network details.
- Avoid uploading complete router configuration backups.
- Do not scan or probe networks without authorization.
- Avoid exposing diagnostic or administrative services publicly.
- Review firewall rules before changing them.
- Keep screenshots and command output free of sensitive information.

## 13. Evidence to Publish

Add the following as the project progresses:

- A verified home network topology diagram.
- Sanitized `ip -br addr` output.
- A documented routing table.
- Connectivity and DNS test results.
- A subnetting example.
- A Docker network diagram, if applicable.
- At least one documented troubleshooting exercise.

## 14. Roadmap

- [ ] Identify network interfaces.
- [ ] Record the actual IP address and subnet prefix.
- [ ] Identify the default gateway.
- [ ] Document DNS configuration.
- [ ] Complete gateway and external connectivity tests.
- [ ] Document a subnetting exercise.
- [ ] Create a verified topology diagram.
- [ ] Inspect Docker networking.
- [ ] Complete a troubleshooting case study.
- [ ] Publish sanitized evidence on GitHub.

## 15. Current Status

**Status:** In progress.

This project is being developed using the existing home network and Linux Mint installation. Network settings, test results, and troubleshooting conclusions will be added as they are verified.

---

Part of the ThinkCentre Home Lab portfolio.
