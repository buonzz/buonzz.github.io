---
layout: post
title: "Vibe Coding From Anywhere: Agentic CLI + Tailscale + Termux"
date: 2025-11-25
categories: [DevOps, AI, Sentry, VSCode]
tags: [devops, productivity, ai]
subtitle: "How I SSH into my home server and talk to my codebase from my phone"
---


## The Problem

Sometimes inspiration (or urgency) hits when you're **not in front of your main machine**.

You want to:

- Inspect a codebase
- Ask questions about existing logic
- Make small edits
- Think through architecture

…but your project lives on a **home server**, behind **double NAT**, and you only have your phone.

Traditional solutions (port forwarding, public SSH exposure) are either fragile or unsafe.

---

## The Stack

This setup works surprisingly well and feels almost unfairly powerful:

- **Fedora Linux server** (home, double NAT)
- **Continue.dev CLI** → agentic AI that understands your codebase
- **DeepSeek Coder** as the LLM backend
- **Tailscale** → zero-config private networking
- **Android phone**
- **Termux** → real Linux shell on mobile

In short:

> 📱 Phone → Termux → SSH → Fedora server → Continue.dev → AI understands your repo

---

## Why Tailscale Solves Double NAT

Double NAT normally kills inbound connections.

Tailscale flips the model:

- Your server makes an **outbound** encrypted connection
- Your phone does the same
- Both devices meet inside a private WireGuard mesh

No port forwarding. No public exposure. No hacks.

From SSH’s perspective, it feels like a local network.

---

## Continue.dev on the Server

On my Fedora server, I run **Continue.dev CLI**, so the AI lives **next to the code**.

This means:

- Full repository context
- Fast file access
- No syncing or partial clones

My `~/.continue/config.yaml` looks like this:

```yaml
name: "local-deepseek"
version: "1.0.0"

models:
  - name: "DeepSeek Coder"
    provider: "openai"
    model: "deepseek-coder"
    apiBase: "https://api.deepseek.com/v1"
    apiKey: "REDACTED"

defaultModel: "DeepSeek Coder"
allowAnonymousTelemetry: false
```

Once authenticated, I can ask questions like:

- “Why is this function structured this way?”
- “What breaks if I change this interface?”
- “Refactor this for clarity”

All over SSH.

---

## Termux: Real Linux on Android

Termux turns your phone into a legit Linux terminal.

Minimal setup:

```bash
pkg install openssh
```

Then SSH straight into the server using its **Tailscale IP**:

```bash
ssh user@100.x.y.z
```

Once connected:

- Run `cn` (Continue.dev CLI)
- Navigate the repo
- Read, reason, and edit code

This isn’t remote desktop cosplay — it’s actual development.

---

## Why This Feels Different

Most “AI coding” setups are:

- Browser-based
- Detached from the real repo
- Context-limited

This one is:

- Terminal-native
- Repo-aware
- Private
- Always-on

You’re not asking an AI to guess your project.

You’re asking it while **standing inside the project**.

---

## When This Is Useful

- Reviewing PRs while commuting
- Debugging production logic away from your desk
- Capturing architectural thoughts before they fade
- Teaching yourself your own codebase

It’s not about replacing real development time.

It’s about **keeping momentum alive**.

---

## Security Notes

- SSH is only reachable via Tailscale
- No public ports exposed
- Keys stay on your devices
- AI API key lives only on the server

This is far safer than exposing SSH to the internet.

---

## Final Thoughts

Agentic CLI + Tailscale + Termux turns your phone into:

> A secure, private, AI-augmented terminal to your home server

Not flashy.

Just extremely effective.

If you already think in terminals, this feels like cheating — in the best way possible.

