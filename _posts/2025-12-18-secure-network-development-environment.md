---
layout: post
title: "Secure Development Environment with Tailscale, Caddy, and dnsmasq"
categories: [infrastructure, security, development]
subtitle: "Test Your Local Sites Publicly Without Publishing Them Online: Here's How"
---

# Secure Development Environment with Tailscale, Caddy, and dnsmasq

This guide explains how to set up a secure development environment using Tailscale VPN, Caddy reverse proxy, and dnsmasq DNS server to provide HTTPS access to local development services across multiple machines.

## Why You Need This Setup

### Mobile Device Testing
Testing your web applications on mobile devices (Android, iOS) requires proper HTTPS endpoints with valid certificates. Unlike desktop machines where you can modify `/etc/hosts`, mobile devices don't allow easy host file modifications. This setup provides:
- **Automatic DNS resolution** on mobile devices through Tailscale
- **Valid HTTPS certificates** that mobile browsers trust
- **No manual configuration** required on each mobile device
- **Consistent URLs** across all testing devices

### Secure Remote Access
You want to access your development environment from anywhere (home, office, coffee shop) without exposing it to the general public. This setup enables:
- **Private network access** through Tailscale VPN from any location
- **No public port forwarding** or firewall rules needed
- **Encrypted connections** for all traffic
- **Access control** through Tailscale authentication
- **No cloud hosting costs** for development environments

## Architecture Overview

The setup consists of three main components working together:

1. **Tailscale VPN** - Provides secure network connectivity between devices
2. **dnsmasq DNS Server** - Resolves custom domain names to the development server
3. **Caddy Reverse Proxy** - Handles HTTPS termination and routing to local services

## How It Works

### Network Flow
```
Client Machine (via Tailscale) 
    ↓ DNS Query (local-api.example.com)
dnsmasq DNS Server (100.x.x.x)
    ↓ Returns server IP
Client connects to Caddy (100.x.x.x:443)
    ↓ HTTPS with internal certificates
Caddy routes to local service (localhost:8080)
```

### Component Roles

**Tailscale VPN:**
- Creates secure mesh network between all devices
- Assigns stable IP addresses (100.x.x.x range)
- Handles authentication and encryption
- Enables split DNS configuration

**dnsmasq DNS Server:**
- Runs on the development server
- Resolves custom domains to server's Tailscale IP
- Configured as nameserver for Tailscale clients
- Provides local DNS resolution without external dependencies

**Caddy Reverse Proxy:**
- Terminates HTTPS with internal certificates
- Routes requests to appropriate backend services
- Adds security headers for proper forwarding
- Handles multiple domains and services

## Setup Process

### 1. Tailscale Configuration

Install Tailscale on all devices and connect them to your tailnet:

```bash
# Install Tailscale (varies by OS)
curl -fsSL https://tailscale.com/install.sh | sh

# Connect to your tailnet
sudo tailscale up
```

### 2. dnsmasq DNS Server Setup

Configure dnsmasq on your development server:

```bash
# Install dnsmasq
sudo apt install dnsmasq

# Create DNS configuration
sudo tee /etc/dnsmasq.d/internal.conf << EOF
# Listen on Tailscale interface
listen-address=127.0.0.1,100.x.x.x
bind-interfaces
no-dhcp-interface=*
domain-needed
bogus-priv
no-resolv
no-poll
no-hosts

# Upstream DNS servers
server=8.8.8.8
server=8.8.4.4

# Local domain mappings
address=/local-api.example.com/100.x.x.x
address=/local-app.example.com/100.x.x.x
EOF

# Start dnsmasq
sudo systemctl enable --now dnsmasq
```

### 3. Caddy Reverse Proxy Setup

Configure Caddy for HTTPS termination and routing:

```caddyfile
# Global settings
{
    http_port 80
    https_port 443
    skip_install_trust
    local_certs
}

# API service
local-api.example.com {
    reverse_proxy localhost:8080 {
        header_up X-Forwarded-Proto https
        header_up X-Forwarded-Port 443
        header_up X-Forwarded-For {remote_host}
        header_up X-Forwarded-Ssl on
        header_up X-Url-Scheme https
        header_up HTTPS on
    }
    tls internal
}

# Web application
local-app.example.com {
    reverse_proxy localhost:3000 {
        header_up X-Forwarded-Proto https
        header_up X-Forwarded-Port 443
        header_up X-Forwarded-For {remote_host}
        header_up X-Forwarded-Ssl on
        header_up X-Url-Scheme https
        header_up HTTPS on
    }
    tls internal
}
```

### 4. Tailscale DNS Configuration

Configure Tailscale to use your server as DNS:

```bash
# Set your server as nameserver for custom domains
tailscale set --nameserver=100.x.x.x
```

## Benefits

### Security
- **Encrypted Traffic**: All communication encrypted via Tailscale
- **No Public Exposure**: Services remain private to your tailnet
- **HTTPS Everywhere**: Internal certificates provide HTTPS for all services
- **Access Control**: Tailscale handles authentication and authorization

### Development Experience
- **Custom Domains**: Use meaningful domain names instead of IP:port
- **Cross-Device Access**: Access services from any device in your tailnet
- **No Certificate Warnings**: Proper HTTPS with trusted internal certificates
- **Easy Service Discovery**: DNS-based service discovery

### Operational Advantages
- **No External Dependencies**: Works without internet for local development
- **Scalable**: Easy to add new services and domains
- **Portable**: Works across different networks and locations
- **Maintainable**: Simple configuration files for all components

## Troubleshooting

### DNS Resolution Issues
```bash
# Test DNS resolution
nslookup local-api.example.com 100.x.x.x

# Check dnsmasq status
sudo systemctl status dnsmasq

# View dnsmasq logs
sudo journalctl -u dnsmasq -f
```

### Certificate Issues
```bash
# Check Caddy status
sudo systemctl status caddy

# View Caddy logs
sudo journalctl -u caddy -f

# Test HTTPS connection
curl -v https://local-api.example.com/
```

### Tailscale Connectivity
```bash
# Check Tailscale status
tailscale status

# Test connectivity
ping 100.x.x.x

# Check DNS configuration
tailscale status --json | grep -A5 DNS
```

## Common Pitfalls

1. **Conflicting /etc/hosts entries** - Remove any localhost entries for your custom domains
2. **DNS caching** - Clear DNS cache after configuration changes
3. **Firewall rules** - Ensure ports 53 (DNS) and 443 (HTTPS) are accessible
4. **Service binding** - Make sure backend services bind to localhost or all interfaces

## Conclusion

This setup provides a robust, secure development environment that scales across multiple services and devices. The combination of Tailscale's mesh VPN, dnsmasq's local DNS resolution, and Caddy's reverse proxy creates a production-like environment for development while maintaining security and ease of use.

The architecture is particularly valuable for:
- **Mobile app development** - Test APIs on real devices without certificate issues
- **Remote development** - Access your dev environment securely from anywhere
- **Team collaboration** - Share development services without public exposure
- **Microservices development** - Use proper domain names instead of localhost:port
- **Cross-device testing** - Consistent HTTPS URLs across all devices
- **Client demos** - Show work-in-progress to clients securely
