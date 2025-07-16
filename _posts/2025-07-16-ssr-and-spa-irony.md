---
title: "SPAs, SSR, and the Great Circle of Web Development"
author: Darwin Biler
date: 2025-07-16
tags: [webdev, php, javascript, frontend, nextjs, humor, opinion]
subtitle: "So we were told to throw away our tried-and-tested SSR stacks, and instead ship megabytes of JavaScript to the client just to render a Hello World."
---

> _"The frontend giveth, and the frontend taketh away."_ — Every backend dev, ever.

I’ve been building web applications since PHP was king, AJAX was revolutionary, and jQuery was the cool kid on the block. In those days, we did this magical thing called **server-side rendering**: generate HTML on the server, send it to the browser, done. Simple. Predictable. Fast.

Then came the **SPA revolution**. JavaScript frameworks promised us:

- Blazing fast UX 🚀
- No more page reloads 🔄
- Code reuse between frontend/backend (spoiler: didn’t happen) 💥

So we were told to throw away our tried-and-tested SSR stacks, and instead ship megabytes of JavaScript to the client just to render a “Hello World.”

## And then... the wheel turned.

After a few years of:

- Broken SEO
- Long initial load times
- Poor accessibility
- Complicated hydration logic
- And way too many `useEffect` bugs...

The frontend community had an epiphany:

> _“Wait... what if we render the page on the **server**... again?”_

Enter **Next.js**, **Nuxt**, **SvelteKit**, and a slew of meta-frameworks that all basically say:

- “Let’s render on the server (SSR)!”
- “Let’s pre-generate pages at build time (SSG)!”
- “Let’s revalidate after deploy (ISR)!”
- “Let’s hydrate client-side (CSR)!”

I call this: **SSR++**, or “SSR but now it’s JavaScript and needs a DevOps team.”

## It’s Not Wrong — Just Funny

Let me be clear: these frameworks aren’t bad. They solve real problems for large apps.

But from the perspective of an old-school PHP dev like me, the whole thing is a little hilarious. We:

- Went full SPA to avoid reloads
- Then added client-side routing
- Then realized we broke SEO and performance
- Then reinvented server rendering
- Then added hydration to patch the mismatch
- Then invented islands, streaming, edge rendering...

And now, what we have looks suspiciously like the same SSR model I was using in 2009 — just with **100x more tooling and build steps**.

## What Did We Actually Gain?

| Feature | MPA (PHP, Rails) | SPA (React/Vue) | SSR++ (Next.js etc) |
|--------|------------------|------------------|---------------------|
| SEO | ✅ | ❌ | ✅ |
| Interactivity | ⚠️ (needs JS) | ✅ | ✅ |
| Dev simplicity | ✅ | ❌ | ❌❌ |
| DX (component logic) | ❌ | ✅ | ✅ |
| First load speed | ✅ | ❌ | ✅ (if done right) |

In the end, we traded **simplicity** for **flexibility**, and **performance** for **interactivity** — only to slowly realize we actually want **all of it**. Which makes sense. But it’s still funny.

## The Takeaway

Not every site needs to be a SPA.

If you're building a **content site**, a **dashboard with minor JS interactivity**, or anything that doesn’t need hyperactive UI transitions — then you probably don’t need 12MB of JavaScript, a hydration strategy, and three rendering pipelines.

Maybe — just maybe — your good old **server-rendered PHP pages**, or something like **HTMX** or **Alpine.js**, are still enough.

Sometimes, evolution is a loop.

---

🧠 **PS:** If you're a new dev and wondering if you missed something by not learning PHP — you didn't. But also, you kind of did.
