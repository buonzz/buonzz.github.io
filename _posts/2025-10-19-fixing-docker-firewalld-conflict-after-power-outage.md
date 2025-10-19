---
layout: post
title: "Fixing Docker and Firewalld Conflict After Power Outage"
date: 2025-10-19 12:00:00 +0800
categories: [docker, linux, troubleshooting]
tags: [docker, firewalld, iptables, fedora, systemd]
slug: fixing-docker-firewalld-conflict-after-power-outage
subtitle: "Learn how to resolve Docker iptables contention with firewalld after unexpected system shutdowns and power fluctuations."
---

# Fixing Docker and Firewalld Conflict After Power Outage

If you're running Docker on a Linux system with firewalld (common on Fedora, CentOS, RHEL), you might encounter a frustrating issue after power outages or unexpected system reboots. Docker containers that were working perfectly suddenly become inaccessible, and you see warnings about "xtables contention" in your logs.

## The Problem: Docker Stops Working After Power Fluctuation

After a system reboot due to power issues, you might notice:

- Docker containers are running but not accessible from the network
- Docker daemon logs show warnings like:
  ```
  dockerd: time="..." level=warning msg="xtables contention detected"
  ```
- Manual workarounds involve disabling Docker's iptables functionality
- You have to manually add firewall rules for every Docker port

## Understanding the Root Cause

The issue occurs because both Docker and firewalld try to manage iptables rules, leading to conflicts. When the system reboots unexpectedly, the timing of service startup can cause this contention.

### The Temporary (But Problematic) Fix

Many online solutions suggest disabling Docker's iptables management entirely:

```bash
echo '{"iptables": false}' | sudo tee /etc/docker/daemon.json
sudo systemctl restart docker
```

While this stops the warnings, it creates a new problem: **you now have to manually manage all Docker port forwarding through firewalld**. Every time you start a new container with exposed ports, you need to add firewall rules manually.

## The Permanent Solution: Proper Integration

Instead of disabling iptables, we can configure Docker and firewalld to work together harmoniously.

### Step 1: Configure Docker Properly

Update your Docker daemon configuration (`/etc/docker/daemon.json`):

```json
{
  "iptables": true,
  "userland-proxy": false,
  "experimental": false
}
```

**Key settings:**
- `"iptables": true` - Allows Docker to manage its own iptables rules
- `"userland-proxy": false` - Reduces resource usage and potential conflicts
- `"experimental": false` - Ensures stability

### Step 2: Configure Firewalld for Docker

Configure firewalld to work with Docker by adding the Docker bridge interface to the trusted zone:

```bash
# Add Docker bridge to trusted zone
sudo firewall-cmd --permanent --zone=trusted --add-interface=docker0

# Enable masquerading for Docker zone (optional but recommended)
sudo firewall-cmd --permanent --zone=docker --add-masquerade

# Reload firewall configuration
sudo firewall-cmd --reload
```

### Step 3: Restart Docker Service

Apply the changes by restarting Docker:

```bash
sudo systemctl restart docker
```

## How This Solution Works

1. **Docker manages its own iptables rules** for container networking and port forwarding
2. **Firewalld trusts the Docker bridge interface**, allowing unrestricted container communication
3. **No more manual port rules** - Docker automatically handles port exposure
4. **Proper network isolation** is maintained while allowing container functionality

## Verification Steps

After applying the solution, verify everything is working:

1. **Check Docker status:**
   ```bash
   sudo systemctl status docker
   ```

2. **Verify firewalld configuration:**
   ```bash
   sudo firewall-cmd --zone=trusted --list-all
   ```

3. **Test container accessibility:**
   ```bash
   # Replace with your container's exposed port
   curl http://localhost:8080
   ```

4. **Check Docker iptables rules:**
   ```bash
   sudo iptables-save | grep DOCKER
   ```

## Benefits of This Approach

- **Automatic port management** - No manual firewall rules needed
- **System stability** - Eliminates iptables contention warnings
- **Future-proof** - New containers work automatically
- **Security maintained** - Proper network isolation
- **No ongoing maintenance** - Set it and forget it

## Troubleshooting Common Issues

### If Docker Fails to Start

Check Docker logs for specific errors:
```bash
sudo journalctl -u docker -f
```

### If Containers Still Aren't Accessible

Verify your Docker network configuration:
```bash
docker network ls
docker ps --format "table {{.Names}}\t{{.Ports}}"
```

### Emergency Rollback

If needed, you can temporarily revert to the manual approach:
```bash
echo '{"iptables": false}' | sudo tee /etc/docker/daemon.json
sudo systemctl restart docker
```

## Conclusion

Power outages and unexpected reboots don't have to mean hours of troubleshooting Docker networking issues. By properly configuring the integration between Docker and firewalld, you can ensure your containers remain accessible and your system stays stable.

This solution has been tested on Fedora Linux but should work on any systemd-based distribution using firewalld. The key is allowing both services to do what they do best while preventing conflicts through proper zone configuration.

**Have you encountered this issue?** Share your experience in the comments below!

---

*This article was written based on real-world troubleshooting experience. If you found it helpful, consider sharing it with others who might be facing similar Docker networking issues.*
