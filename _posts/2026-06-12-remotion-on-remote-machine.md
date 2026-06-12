---
layout: post
title: "Run Remotion on a Remote Machine and Preview It Locally"
date: 2026-06-12
slug: remotion-remote-machine
subtitle: "Edit and preview Remotion videos on a headless server from your local browser"
---

# Run Remotion on a Remote Machine and Preview It Locally

[Remotion](https://remotion.dev) lets you create videos using React. It's powerful, but running the Remotion Studio (the visual editor) on a headless remote server can feel tricky — there's no browser to open and no display to render to. This post walks through exactly how to set it up so you can develop Remotion projects on your remote machine and preview everything from your local browser.

---

## 1. The Setup

I assume you already have a Remotion project on your remote machine. If not, create one:

```bash
npx create-video@latest my-project
cd my-project
```

Make sure Node.js is installed (v18+). Remotion also needs Chrome/Chromium on the server. If it's not installed, Remotion will download its own — but you can install it explicitly if you prefer:

```bash
# Ubuntu/Debian
sudo apt install chromium-browser
```

---

## 2. Start Remotion Studio on the Remote Machine

SSH into your remote machine and start Remotion Studio:

```bash
ssh user@your-remote-machine
cd /path/to/your-remotion-project
npm run dev
```

By default, Remotion Studio starts on `http://localhost:3000`. You'll see output like:

```
Remotion Studio
  http://localhost:3000
```

At this point, the server is running, but you can't open `localhost:3000` on your local machine because `localhost` refers to the remote machine itself.

---

## 3. SSH Tunnel: Bridge the Gap

The trick is **SSH port forwarding** (also called SSH tunneling). On your **local machine**, open a new terminal and run:

```bash
ssh -L 3000:localhost:3000 user@your-remote-machine
```

Here's what this does:

| Part | Meaning |
| :--- | :--- |
| `-L 3000:localhost:3000` | Forward local port 3000 to remote's `localhost:3000` |
| `user@your-remote-machine` | Your SSH login |

Now, any traffic hitting your **local** `http://localhost:3000` gets securely tunneled through SSH to the remote machine's `localhost:3000` — where Remotion Studio is listening.

Open your local browser and go to **`http://localhost:3000`**. You should see the Remotion Studio interface.

---

## 4. VS Code Remote SSH (Even Easier)

If you use VS Code with the **Remote - SSH** extension, port forwarding is automatic:

1. Open your remote folder in VS Code (`Ctrl+Shift+P` → "Remote-SSH: Connect to Host...")
2. Open the terminal in VS Code and run `npm run dev`
3. VS Code detects the port and shows a notification: *"Your application running on port 3000 is available."*
4. Click "Open in Browser" or manually visit `http://localhost:3000`

No `ssh -L` needed — VS Code handles tunneling behind the scenes.

---

## 5. Rendering Without Preview

If you just want to render a video file and download it:

```bash
# On the remote machine
npx remotion render src/index.ts output.mp4
```

Then copy it to your local machine:

```bash
# On your local machine
scp user@your-remote-machine:/path/to/remotion-project/output.mp4 .
```

Or use `rsync` for larger files:

```bash
rsync -avz user@your-remote-machine:/path/to/remotion-project/output.mp4 .
```

---

## 6. Persist the Tunnel (Optional)

If you want the tunnel to stay alive even when you close the terminal, use `autossh` or a `-o ServerAliveInterval` flag:

```bash
ssh -L 3000:localhost:3000 -o ServerAliveInterval=60 user@your-remote-machine
```

Or run the Remotion Studio in a `tmux`/`screen` session so it survives disconnects:

```bash
tmux new -s remotion
npm run dev
# Ctrl+B, D to detach
```

---

## Summary

| Goal | Command |
| :--- | :--- |
| Start Remotion Studio on remote | `npm run dev` |
| Preview locally | `ssh -L 3000:localhost:3000 user@host` then open `http://localhost:3000` |
| Render video on remote | `npx remotion render src/index.ts out.mp4` |
| Download video | `scp user@host:path/to/out.mp4 .` |
| Persistent session | Use `tmux` on remote |

That's it. No display server, no VNC, no X11 forwarding — just SSH and a browser.
