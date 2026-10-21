
# 02 — Docker Infrastructure Stack

**Project:** ThinkCentre Home Lab  
**Host:** Lenovo ThinkCentre M91p  
**Operating System:** Linux Mint  
**Technologies:** Docker, Docker Compose, Linux networking

## 1. Overview

This project focuses on running and managing containerized services on my Linux home lab server.

The objective is to understand how Docker containers work, how services communicate, how application data persists, and how deployments can be reproduced using configuration files.

The project uses existing hardware and free software.

## 2. Objectives

- Understand Docker images, containers, and registries.
- Manage containers using the Docker CLI.
- Define services using Docker Compose.
- Understand port mappings and container networking.
- Configure persistent storage using volumes.
- Inspect logs and troubleshoot failed containers.
- Test service restart behaviour and data persistence.
- Document deployment and recovery procedures.

## 3. Environment

| Component | Details |
|---|---|
| Hardware | Lenovo ThinkCentre M91p |
| Operating System | Linux Mint |
| Container Runtime | Docker |
| Orchestration | Docker Compose |
| Network | Existing home router |
| RAM | Exact capacity to be verified |

## 4. Inspect the Existing Environment

Before deploying anything new, inventory the current Docker setup.

```bash
docker --version
docker compose version
docker ps
docker ps -a
docker images
docker network ls
docker volume ls
docker system df
```

Document existing containers, their purpose, published ports, volumes, and restart policies.

Do not stop existing services or delete volumes without understanding their purpose.

## 5. Project Architecture

The intended architecture is:

```text
Home Network
     |
Home Router
     |
ThinkCentre M91p
     |
Linux Mint
     |
Docker Engine
     |
Docker Compose Project
     |
Containerized Application
     |
Persistent Data Volume
```

The final diagram should reflect the actual deployment rather than assumed services.

## 6. Repository Structure

```text
02-docker-stack/
├── README.md
├── compose.yaml
├── .env.example
├── .gitignore
├── docs/
│   ├── architecture.md
│   ├── deployment.md
│   └── troubleshooting.md
└── screenshots/
    └── README.md
```

Only include configuration and documentation files that are actually needed.

## 7. Deployment

The stack will be defined using Docker Compose.

Before starting a deployment:

```bash
docker compose config -q
```

Review the configuration and confirm that ports, volumes, and environment variables are correct.

Start the stack:

```bash
docker compose up -d
```

Inspect its status:

```bash
docker compose ps
docker compose logs --tail 100
```

Stop the services without deleting their data:

```bash
docker compose down
```

**Important:** Review any existing deployment before running these commands. Use the appropriate project directory, and do not use volume-deletion flags unless you intentionally want to remove stored application data.

## 8. Container Networking

Document:

- Container names and purposes.
- Internal Docker networks.
- Published host ports.
- Communication between services.
- Which services are accessible from the local network.

Useful commands:

```bash
docker network ls
docker network inspect NETWORK_NAME
docker port CONTAINER_NAME
```

Replace the example names with actual values.

Only publish the ports that are necessary. Do not expose administrative dashboards or sensitive services to the public internet without an appropriate security design.

## 9. Persistent Storage

Persistent storage allows application data to survive container replacement.

Document:

- Which services require persistent data.
- The volumes or bind mounts used.
- Where data is stored.
- Backup procedures.
- Restoration and verification procedures.

Inspect existing volumes:

```bash
docker volume ls
docker volume inspect VOLUME_NAME
```

### Persistence Test

1. Create or modify test data using the application.
2. Verify that the data is present.
3. Recreate the container without deleting its volume.
4. Confirm that the data remains available.
5. Record the results.

Perform tests only on a service and data set you control.

## 10. Resource Monitoring

Because the ThinkCentre has limited RAM, monitor container resource usage.

```bash
docker stats
free -h
df -h
```

Record:

- Container memory consumption.
- CPU usage.
- Disk usage.
- Service response and stability.
- Any resource-related failures.

Choose lightweight services and avoid running more containers than the hardware can support comfortably.

## 11. Troubleshooting

Useful commands:

```bash
docker ps -a
docker logs --tail 100 CONTAINER_NAME
docker inspect CONTAINER_NAME
docker compose ps
docker compose logs
```

For each incident, document:

1. The symptoms.
2. The diagnostic commands.
3. The suspected root cause.
4. The change made.
5. The test used to verify the resolution.
6. Lessons learned.

### Incident Log

| Date | Issue | Root Cause | Resolution | Status |
|---|---|---|---|---|
| To be recorded | To be recorded | To be verified | To be documented | Pending |

## 12. Security Practices

- Never commit real passwords, API keys, or tokens.
- Keep local `.env` files out of Git.
- Provide `.env.example` with placeholder values only.
- Use trusted container images.
- Avoid privileged containers unless strictly necessary.
- Limit published ports and container permissions.
- Review image updates before deploying them.
- Back up important volumes and test recovery.

## 13. Evidence to Publish

Add real evidence as the project develops:

- Docker version and Compose version.
- Sanitized `docker compose ps` output.
- The Compose configuration.
- An architecture diagram.
- Container networking and port mapping details.
- Successful persistence test results.
- Resource usage observations.
- Troubleshooting notes.

## 14. Roadmap

- [ ] Inventory existing containers and volumes.
- [ ] Document the current Docker environment.
- [ ] Create a reproducible Compose deployment.
- [ ] Document container networking and port mappings.
- [ ] Verify persistent storage.
- [ ] Test container restart behaviour.
- [ ] Measure resource usage.
- [ ] Document a troubleshooting case.
- [ ] Test backup and recovery.
- [ ] Publish sanitized deployment evidence.

## 15. Current Status

**Status:** In progress.

Existing Docker services will be documented before making changes. New deployments and test results will be recorded only after they have been implemented and verified.

---

Part of the ThinkCentre Home Lab portfolio.
