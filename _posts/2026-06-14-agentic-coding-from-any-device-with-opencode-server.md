---
layout: post
title: "Agentic Coding From Any Device: opencode Server Behind Tailscale"
date: 2026-06-14
slug: opencode-server-tailscale
subtitle: "Run opencode on your Linux home server and access it from any browser — phone, tablet, Chromebook, anything"
categories: [DevOps, AI, Productivity]
tags: [opencode, tailscale, agentic-ai, homelab, devops, productivity]
---

## The Problem

Every "AI coding" tool assumes you're sitting in front of a powerful machine with an IDE installed.

But what if you're:

- On the couch with a tablet
- At a coffee shop with a Chromebook
- Commuting with only your phone
- Traveling light without a laptop

The usual answer is: SSH in, use a terminal-based AI tool. That works — I've done it — but it still requires a terminal emulator app, and the experience is inherently text-only.

**What if you could get the full agentic coding chat experience — the same one you'd get in VS Code or Claude's web UI — inside any browser, on any device, without installing anything?**

That's what this setup does.

---

## The Stack

- **Fedora Linux server** (my home lab, behind double NAT)
- **opencode** — a headless server that exposes an agentic coding chat via HTTP
- **DeepSeek** as the LLM backend
- **Tailscale** — encrypted WireGuard mesh VPN
- **Any browser** on phone, tablet, laptop, whatever

The flow:

> 📱 Browser → Tailscale IP → opencode server → DeepSeek API → agentic coding

---

## Why opencode Instead of SSH + Terminal AI

I wrote previously about using Continue.dev CLI over SSH via ConnectBot. That setup is still great for quick terminal queries. But opencode is a different paradigm:

| Feature | SSH + Continue CLI | opencode Server |
|---|---|---|
| Client | ConnectBot / terminal | **Any browser** |
| Interface | Text-only terminal | **Rich chat UI** |
| Device support | Android only | **Every device** |
| File editing | Manual in terminal | **Agent can edit files** |
| Setup on client | Install app | **Open a URL** |
| Context length | Limited by scrollback | **Full HTTP session** |

The browser interface means I can hand my phone to a non-technical colleague and say "just type what you need."

---

## Setting It Up

### 1. Install opencode

On your server:

```bash
curl -fsSL https://opencode.ai/install.sh | sh
```

Or grab the binary from the [releases page](https://github.com/anomalyco/opencode/releases).

### 2. Configure the server

opencode can run as an HTTP server. You configure it in `~/.config/opencode/opencode.json`:

```json
{
  "server": {
    "port": 8094,
    "host": "100.x.x.x"
  },
  "model": {
    "provider": "deepseek",
    "model": "deepseek-chat"
  }
}
```

The `host` should be your Tailscale IP so it only listens on the VPN interface — never on a public-facing port.

### 3. Start the server

```bash
opencode server
```

You'll see:

```
opencode server running on http://100.x.x.x:8094
```

### 4. Access from any device

Open your browser and navigate to the Tailscale IP:

```
http://100.x.x.x:8094
```

That's it. No install, no app, no SSH key setup on the client. You get the full agentic coding chat — the AI can read your codebase, edit files, run commands, and reason about your project.

---

## Why Tailscale Is Critical

My home server is behind **double NAT** (ISP router + my own router). Traditional approaches would require:

- Port forwarding (fragile, security risk)
- A VPS relay (cost, latency)
- Dynamic DNS + public exposure (dangerous)

Tailscale solves all of this:

- **Zero-config NAT traversal** — devices meet in a WireGuard mesh
- **No open ports** — the server initiates the outbound connection
- **Encrypted by default** — end-to-end WireGuard encryption
- **Access controls** — only devices in your tailnet can reach the server

The server binds to the Tailscale IP, so the service is never exposed to the public internet. It's like having a private LAN that spans the globe.

---

## Real-World Usage

I use this constantly. Here are the scenarios that make it worth it:

### Quick fixes from my phone

A bug gets reported while I'm out. I open Chrome on my phone, type in the Tailscale URL, and tell opencode:

> "Find the error handler in the auth module and add logging for the token expiry case"

It reads the code, makes the edit, and I'm done in 60 seconds.

### Reviewing PRs on a tablet

I open the repo, ask opencode to summarize the diff, then ask follow-up questions about specific functions. No laptop needed.

### Pair debugging with a colleague

I share my screen (or just send them the URL if they're on my tailnet). They can see the AI reasoning in real time.

### Coding from a Chromebook

Chromebooks are great for browsing but terrible for local development. This turns any Chromebook into a full agentic coding workstation — just open a tab.

---

## Security

- **No public ports** — opencode binds to the Tailscale interface only
- **No API keys on clients** — the LLM API key lives on the server
- **No data leaves your tailnet** — everything stays inside the WireGuard mesh
- **Tailscale ACLs** — you can restrict which devices can reach the opencode port

If someone gets access to your Tailscale network, you have bigger problems — but even then, opencode's server has no authentication built in, so I add a simple reverse proxy auth or just rely on Tailscale ACLs being strict.

---

## What This Enables

This setup fundamentally changes *where* and *when* you can do serious coding work.

You're no longer tied to a specific machine with a specific IDE. The AI and the codebase live together on the server, and every device becomes a thin client with full agentic capabilities.

The phone in your pocket is now a supercomputer for software engineering.

Not "run a terminal and type commands." A full, interactive, agentic coding assistant — in a browser tab.

---

## Final Thoughts

I've been through many iterations of this:

1. **SSH + vim** — works, but no AI
2. **SSH + Continue CLI** — great, but terminal-only
3. **opencode server + Tailscale** — browser-based agentic coding from anything

The third one is by far the most practical. It requires zero setup on the client, works on every device I own, and gives me the same agentic coding experience I'd get sitting at my desk — just from a browser tab on a phone.

If you have a Linux server and a Tailscale network, try it. You might find yourself reaching for your phone more often than you expect.
