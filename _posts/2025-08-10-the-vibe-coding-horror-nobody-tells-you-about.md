---
title: "The vibe coding horror nobody tells you about"
subtitle: "Before you dive into the hype of vibe coding, read this first!"
---

You know those coding horror stories people don’t warn you about? Here’s mine—navigating AI coding tools felt like stumbling through a haunted house blindfolded.

## Cursor AI: Card declined, usage blocked, no mercy

- I tried Cursor and was greeted with the dreaded **"You've hit your usage limit"**—fine, I get it. But when I tried upgrading to Pro... **“Your card has been declined.”** Only Alipay and Cash App Pay were available; if you're not in the U.S., good luck.

![Cursor payment declined screenshot](/img/cursor1.png)  
![Cursor usage limit screenshot](/img/cursor2.png)

- Community forums are full of complaints about this. One user from Italy shared, “Trying to buy a monthly pro, but it will decline my card for no reason…” even though the card works elsewhere.  
- Issues persist worldwide. A user in the Philippines lamented, “Been paying for 6 months … suddenly all payment methods not working for me here.”  
- Others report that their card issuer has blacklisted Cursor due to “fraud detection”—completely blocking access.  
- Even Alipay is unreliable for some users: multiple deductions reported without Pro access or refunds issued.  
- One shared frustration: “The payment still would not go through. My card works perfectly everywhere else, just not here.”

**Warning:** Cursor’s payment system can be a minefield. If you're outside the U.S., or sometimes even inside it, be prepared for decline messages, slow refunds, and limited payment options.

## Claude Code: Phone-number maze and temp-roadblocks

- Claude Code forces phone verification—but many hit a roadblock named **“phone_number_temp_blocked.”** Users report being blocked mid-onboarding despite multiple attempts.

![Claude AI phone verification error](/img/claude.jpg)

- Some get through by logging off and trying again later or using a different device—pure luck.  
- Workarounds include using virtual phone numbers or signing up via Google instead of SMS—but these are band-aids, not official fixes.

**Warning:** Claude’s phone system can lead straight to frustration. You may never see that verification code—be ready with alternatives if your region isn't supported.

## GitHub Copilot: Quota nightmare—even with paid plans

- I hit a wall: **“You have exceeded your premium request allowance…”** even on a paid account.

![GitHub Copilot quota exceeded error](/img/copilot.png)

- Copilot Pro+ gives a limited number of premium-model requests per billing cycle. Use them up and you’re downgraded to a lower model.  
- Some users reported the limit triggering immediately after purchase—or before using it at all. Students on educational plans reported similar surprises.  
- Quotas often track billing cycles (not calendar months), which adds confusion.  
- Premium-model multipliers can annihilate your budget fast (e.g., some premium models consume many “request units” per call).  
- People report being blocked after only a handful of calls and then having to wait weeks for resolution.

**Warning:** Even if you're paying, hidden quota mechanics can interrupt a project mid-work.

## Windsurf

I keep seeing this error:

```
Resource exhausted: Encountered retryable error from model provider: Global rate limit reached for this model due to high demand. Your request was not processed and your credits have been refunded. Please upgrade to a pro account for priority access or try again in a few minutes.: Rate limit error
```

![Windsurf error](/img/windsurf.png)

## Anthropic

This request would exceed the rate limit for your organization.  
![Anthropic rate limit error](/img/anthropic.png)

---

### TL;DR – Friendly Warnings for the Unprepared

| Tool            | Common Pitfall                                            | Real-World Reports                           |
|-----------------|------------------------------------------------------------|-----------------------------------------------|
| **Cursor AI**    | Payment failures, limited options, poor refund support    | Global users stuck with declined cards |
| **Claude Code**  | Razor-tight phone verification, vague blocks              | Onboarding blocked by phone verification issues |
| **GitHub Copilot** | Hidden quota limits—even paid can't use premium models  | Quotas triggered unexpectedly; students affected |

---

### My Advice to Future Users

- **Cursor:** Have backup payment methods (virtual cards, international-friendly) ready. Test payments before committing.  
- **Claude:** Try Google sign-in or a virtual number as a fallback; test onboarding early.  
- **Copilot:** Track usage closely from day one, and budget for premium-model consumption.

---

### The Escape Hatch: No More Limits, No More Payment Nightmares

After trying (and failing) to make all these tools behave, I found a simple, reliable alternative that fixed the problems for me:

1. Install **Roo** in Visual Studio Code (search for *Roo* in the VS Code Marketplace or [install it here](https://marketplace.visualstudio.com/items?itemName=roocode.roo)).  
2. Sign up for a free account at [OpenRouter](https://openrouter.ai/).  
3. Generate an **API key** in your OpenRouter dashboard.  
4. Open Roo in VS Code and enter your OpenRouter API key in Roo’s settings.

That’s it—no declined payments, no SMS gatekeeping, no hidden quota cliffs. Roo + OpenRouter gave me smooth, uninterrupted AI-assisted coding.
