# Documentation

This directory contains documentation related to the server infrastructure, system administration, development workflow, and maintenance of the project.

The documentation is organized by purpose so that configuration details, operational procedures, troubleshooting cases, and project tasks are kept separate.

## Documentation structure

### `server.md`

Contains the current server configuration and infrastructure documentation.

This includes information about:

- Server hardware and operating system
- Network interfaces and configuration
- SSH configuration and hardening
- Firewall configuration
- Time synchronization
- Installed and configured infrastructure components
- Deployment and service configuration

This file describes the intended and current state of the server.

### `checklist.md`

Contains checklists for server setup, configuration, security, deployment, and maintenance.

The checklist is used to track completed and pending tasks throughout the project.

### `commands.md`

Contains useful commands used for server administration, troubleshooting, diagnostics, and maintenance.

Commands are grouped by purpose and include short explanations where necessary.

### `troubleshooting.md`

Contains documented troubleshooting cases encountered during the project.

Each case describes the problem, investigation process, relevant commands, findings, and the final solution or current status.

This file is intended to document not only the solution, but also the reasoning and diagnostic process.

### `inventory/`

Contains snapshots of the server state collected during different stages of the project.

Inventory files may include information about:

- Installed packages and snaps
- Running services
- Listening network sockets
- Network interfaces and routes
- Netplan configuration
- Failed systemd units
- Other relevant system state

Inventory snapshots are used to track infrastructure changes and provide a reference for audits and troubleshooting.

## Documentation principles

The documentation is maintained alongside the project and updated as the infrastructure changes.

Environment-specific sensitive information is sanitized before being committed to the public repository. This includes credentials, private keys, secrets, and other information that should not be publicly exposed.

The goal of this documentation is not only to describe the final configuration, but also to record the process of building, securing, troubleshooting, and maintaining the infrastructure.
