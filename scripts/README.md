
# Home Lab Automation Scripts

A collection of Bash scripts developed for my Lenovo ThinkCentre M91p home lab running Linux Mint.

These scripts automate routine system checks, provide basic infrastructure visibility, and support learning Linux administration, networking, and Docker.

## Objectives

- Automate repetitive Linux administration tasks.
- Monitor CPU load, memory, and disk usage.
- Check system service health.
- Inspect Docker containers.
- Practise Bash scripting and error handling.
- Build reusable tools for infrastructure troubleshooting.

## Scripts

| Script | Purpose | Status |
|---|---|---|
| `system-health.sh` | Report system resources and health | Initial version |
| `network-check.sh` | Inspect network configuration and connectivity | Planned |
| `docker-status.sh` | Summarize Docker container status | Planned |

## Requirements

- Linux Mint or a compatible Linux distribution.
- Bash shell.
- Standard Linux command-line utilities.
- Docker installed for Docker-specific scripts.

Individual scripts may have additional requirements.

## Usage

Make a script executable:

```bash
chmod +x system-health.sh
```

Run it:

```bash
./system-health.sh
```

Run scripts from the repository's `scripts/` directory.

## Development Principles

- Keep scripts small and readable.
- Use safe defaults and clear error messages.
- Avoid unnecessary root privileges.
- Never hardcode passwords, tokens, or private information.
- Do not make destructive system changes without explicit confirmation.
- Test scripts before publishing them.
- Document expected output and known limitations.

## Roadmap

- [ ] Complete the system health report.
- [ ] Add network diagnostics.
- [ ] Add Docker status reporting.
- [ ] Test error handling.
- [ ] Add usage examples and sample output.
- [ ] Document tested operating systems and dependencies.

---

Part of the ThinkCentre Home Lab portfolio.
