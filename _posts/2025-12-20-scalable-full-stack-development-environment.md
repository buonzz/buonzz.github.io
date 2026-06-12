---
layout: post
title: "Scalable Full-Stack Development Environment"
date: 2025-12-20 14:45:00 +0800
categories: ["development", "devops", "infrastructure"]
subtitle: "A Thin-Client, Container-First Approach to Developer Resilience"
---

# Scalable Full-Stack Development Environment

*A thin-client, container-first approach to building a resilient, portable development workflow.*

As a full-stack developer, I’ve repeatedly run into problems that don’t show up in tutorials but absolutely show up in real life: OS updates breaking setups, machines crashing mid-work, environments drifting away from production, and the constant fear that a reformat means rebuilding everything from scratch.

Over time, I intentionally designed a development environment that prioritizes **stability, portability, and recoverability** over convenience. This post documents that setup—not as a theoretical best practice, but as a system I actively use.

This setup is especially relevant for solo developers, indie hackers, and small teams who want their development environment to behave more like infrastructure than a fragile personal workstation.

---

## The Pain Points

### 1. System Instability
No matter how powerful your PC, laptop, or Mac is, you are always one buggy program away from crashing your entire system. Memory leaks, runaway Docker containers, bad drivers, or misbehaving apps can freeze or reboot your machine—taking your development work with it.

### 2. Lack of Portability
I need to be able to resume my work from *any* device—mobile phone, tablet, laptop—anytime, anywhere. Traditional setups assume you are sitting in front of the same machine that contains your entire development state.

### 3. Environment Inconsistency
Reproducing production environments locally is notoriously difficult. Slight differences in OS, libraries, PHP or Node versions, and system packages lead to the classic problem:

> “It works on my machine.”

### 4. OS-Level Disruptions
OS updates frequently break development tools or require reconfiguration. Whether it’s macOS, Windows, or Linux, updates tend to reset assumptions your workflow quietly relied on.

### 5. Hardware Failures
A corrupted disk, damaged SSD, malware incident, or forced reformat means rebuilding everything. Even with backups, the *environment itself* is often undocumented and unreproducible.

---

## The Solution: A Distributed, Containerized Approach

Instead of treating my primary computer as both a **workstation and a server**, I separated responsibilities.

This effectively turns my local machine into a **thin client**, while a dedicated Linux machine becomes the **authoritative source of compute, state, and configuration**.

---

## Hardware Configuration

I use two inexpensive computers, each focused on what it does best.

### 1. macOS Machine (Thin Client)

- **Purpose**: User interaction only (screen, keyboard, mouse)
- **Installed software**:
  - Google Chrome
  - Sequel Ace
- **Philosophy**:
  - No compilers
  - No runtimes
  - No databases
  - Nothing critical lives here

If this machine dies, is reformatted, or updated aggressively, nothing important is lost.

### 2. Linux Server (Development Infrastructure)

- **OS**: Fedora Linux Server
- **Responsibilities**:
  - Running all server software
  - Storing all code
  - Hosting databases
  - Managing containers

Installed components include:

- Docker Engine + Docker Compose
- Caddy (reverse proxy and TLS)
- Visual Studio Code Server
- Continue.dev CLI
- Git
- Tailscale

This machine is where *state* lives.

---

## Network Architecture

To make local development fast and predictable, both machines connect to a dedicated router, while the ISP router is placed upstream.

- macOS device → LAN
- Linux server → LAN
- ISP router → WAN

### Why This Matters

- Local traffic never passes through the ISP router
- Lower latency and fewer bottlenecks
- Isolation from other devices on the ISP network
- Reduced risk of accidental exposure

This results in **double NAT**. While double NAT is not perfect security, it provides meaningful isolation and reduces attack surface and network noise.

### Built-In Internet Failover

The router supports dual WAN inputs. This allows me to attach a secondary connection (for example, prepaid home WiFi) and automatically fail over if the primary wired connection goes down.

Connectivity becomes *infrastructure*, not a single point of failure.

---

## Development Workflow

### Code Editing Anywhere (Code Server as PWA)

I never edit code directly on macOS.

Instead, I access Visual Studio Code Server running on the Linux machine via a browser. By clicking the browser’s **Install** button, Code Server becomes a Progressive Web App.

From the user’s perspective, it behaves like a native application—but all computation and files remain on the server.

I’ve resumed work from:
- Another laptop
- A tablet
- A mobile phone
- An airport gate in another country

Nothing changes except the screen size.

---

### Container-First, Host-Nothing Philosophy

All development dependencies live in containers.

- No PHP installed on the host
- No Node.js installed on the host
- No databases installed on the host

Each project defines exactly what it needs.

If Project A needs PHP 7.4 and Project B needs PHP 8.3, they coexist without conflict—because the host never knows PHP exists.

Docker is not just a tool here; it is the **contract**.

---

### Reverse Proxy and HTTPS with Caddy

When hosting multiple applications on the same machine:

- Caddy routes domains to the correct container
- Each service is reachable via HTTPS
- Self-signed certificates are handled automatically

This allows realistic local development that closely mirrors production behavior.

---

## Resilience by Design

### OS Updates Become Non-Events

When macOS updates arrive, I update immediately.

Nothing breaks—because nothing critical lives there. After the update, I reconnect to Code Server and continue working exactly where I left off.

### Hardware Failures Are Contained

If the macOS machine fails entirely, any device with a browser becomes a replacement. The development environment itself remains intact.

---

## Remote Access from Anywhere

### Tailscale VPN

A Tailscale daemon runs on the Linux server, exposing a secure private IP.

This enables:
- Secure access without port forwarding
- Encrypted connections from anywhere
- Zero-trust networking

### Mobile and Agentic CLI Access

With Tailscale enabled:

1. I connect from a mobile device
2. Open a terminal client
3. Access Continue.dev CLI
4. Interact with the codebase using agentic workflows

This works whether I’m at home, in a café, or traveling internationally.

---

## Off-Premise Backups: Planning for Total Failure

Even with all the precautions above, I assume one thing will eventually happen:

> The Linux server will fail catastrophically.

Fire, power surge, disk failure, or simple human error — local infrastructure is still local infrastructure. To handle this, I treat **off‑premise backups as a first‑class component** of the system.

### Rclone + Backblaze B2

I use **rclone** to synchronize critical data from the Linux server to an off‑site object storage provider.

Rclone supports Backblaze B2 natively, so I configure a remote that points directly to a B2 bucket. From there, scheduled jobs handle backups automatically.

What gets backed up:

- SQL database dumps (MySQL, PostgreSQL, etc.)
- Entire Git repositories (including all branches)
- Project stack repositories (`*-stack`)
- Configuration files that define the environment

What does *not* get backed up:

- Running containers
- Build artifacts
- Anything that can be recreated from Dockerfiles

The goal is simple: **preserve state, not runtime**.

### Recovery Scenario

If the Linux server is completely destroyed:

1. Provision a new Linux machine
2. Install Docker, Docker Compose, and rclone
3. Restore repositories and SQL dumps from Backblaze B2
4. Run `docker compose up`

The entire development environment — including code, history, and data — is reconstructed without relying on any single physical machine.

This turns a catastrophic failure into a recoverable inconvenience.

---

## Project Structure: The “Super-Repo” Pattern

For each project, I create a repository named:

```
[project]-stack
```

Example structure:

```
helloworld-stack/
├── repos/
│   ├── api.example.com
│   ├── app.example.com
│   ├── mysql-migration-scripts
│   └── helloworld-mcp-server
├── docker-compose.yml
├── docker/
├── .gitignore
├── .amazonq/rules
└── .roocode/rules
```

### Why Submodules Instead of a Monorepo?

Each submodule retains its own:
- Git history
- CI/CD pipeline
- Deployment lifecycle

The stack repository simply *orchestrates* them.

### Benefits

- One repository defines the entire system
- Easy onboarding
- Fully reproducible environments
- AI tools gain structured, project-level context

---

## Who This Setup Is (and Isn’t) For

### This Setup Is For:
- Developers who value stability over convenience
- People maintaining multiple long-lived projects
- Anyone tired of rebuilding environments

### This Setup May Be Overkill If:
- You only write small scripts
- You rely heavily on native GUI tooling
- You prefer managed cloud IDEs

---

## Core Philosophy

Treat development machines as disposable clients.

Preserve state only where it matters.

Once you adopt this mindset:
- OS updates stop being scary
- Hardware failures become inconveniences
- Your environment becomes portable by default

---

## Getting Started

1. Start with a dedicated Linux machine (even low-powered hardware works)
2. Install Docker and Docker Compose
3. Set up Code Server
4. Configure Tailscale
5. Create your first `[project]-stack` repository

---

This approach transformed my workflow from something fragile into something durable. I spend far less time fixing my environment—and far more time actually building software.

