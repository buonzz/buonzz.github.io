---
title: Fixing "Remote Controller Not Detected" Error with DJI RC-N3 and SimuDrone on Android
date: 2025-07-05
tags: [dji, simudrone, android, troubleshooting, drone-simulator]
---

When I tried using **SimuDrone**—a drone flight simulator—with my **DJI RC-N3** controller connected to my Android phone, I ran into this frustrating error:

![SimuDrone Error - Remote Controller Not Detected](/assets/simudrone-remote-not-detected.jpg)

> **Remote Controller not detected.  
Please connect to remote controller and restart SimuDrone. Or, you can enable virtual joysticks to play.**

Even though my USB-C cable was working and **DJI Fly** would launch automatically upon connection, **SimuDrone failed to detect the remote**.

---

## 🔍 Root Cause

The problem was caused by setting **DJI Fly** as the **default app** to handle USB connections from the remote. Because of this, Android was automatically routing the USB accessory to DJI Fly—**blocking SimuDrone** from accessing the controller.

---

## ✅ How I Fixed It

### Option 1: Uninstall & Reinstall SimuDrone

This is the quick-and-dirty fix:

1. Uninstall **SimuDrone**.
2. Reinstall it from the Play Store.
3. Reconnect the **DJI RC-N3** via USB-C.
4. When Android prompts you with an **"Open with"** dialog, choose **SimuDrone**.
5. 🎮 Done! SimuDrone now detects the controller.

### Option 2: Clear Default App Settings

If you want to keep both apps installed without constantly reinstalling:

1. Go to `Settings > Apps > DJI Fly`
2. Tap on **"Set as default"**
3. Choose **“Clear defaults”**

Now, the next time you plug in your RC-N3, Android will ask which app to open—choose **SimuDrone** instead of DJI Fly.

---

## 🛠️ Additional Tip

If you want to switch back to DJI Fly after a SimuDrone session, you can always reconnect the controller and choose DJI Fly when prompted—or manually launch it later.

---

## 🚁 Why This Matters

Simulators like **SimuDrone** are a safe and cost-effective way to practice drone control skills. If you're using real hardware like the **DJI RC-N3**, knowing how Android handles USB input routing is crucial to avoid these app conflicts.

Hopefully, this post saves you the same confusion I faced. Happy flying!
