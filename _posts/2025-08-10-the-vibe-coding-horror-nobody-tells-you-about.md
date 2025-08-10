---
title: "The vibe coding horror nobody tells you about"
---

You know those coding horror stories people don’t warn you about? Here’s mine—navigating AI coding tools felt like stumbling through a haunted house blindfolded.

## Cursor AI: Card declined, usage blocked, no mercy

- I tried Cursor and was greeted with the dreaded **"You've hit your usage limit"**—fine, I get it, fair enough. But when I tried upgrading to Pro... **“Your card has been declined.”** Only AliPay and Cash App Pay were available; if you're not in the U.S., good luck.

![Cursor payment declined screenshot](/img/cursor1.png)
![Cursor usage limit screenshot](/img/cursor2.png)

- Community forums are full of complaints about just that. One user from Italy shared, “Trying to buy a monthly pro, but it will decline my card for no reason…” even though the card works elsewhere ([forum.cursor.com](https://forum.cursor.com/t/cursor-wont-take-my-money/19406?page=2&utm_source=chatgpt.com)).  
- Issues persist worldwide. A user in the Philippines lamented, “Been paying for 6 months … suddenly all payment methods not working for me here” ([forum.cursor.com](https://forum.cursor.com/t/cursor-wont-take-my-money/19406?page=2&utm_source=chatgpt.com)).  
- Others report that their card issuer has blacklisted Cursor due to “fraud detection”—completely blocking access ([forum.cursor.com](https://forum.cursor.com/t/cursor-ai-on-blacklist-at-my-bank/24662?utm_source=chatgpt.com)).  
- Even Alipay is unreliable. Users report multiple deductions with no Pro access granted or refunds issued ([forum.cursor.com](https://forum.cursor.com/t/using-alipay-to-subscribe-the-fee-was-successfully-deducted-but-the-pro-was-not-opened-prompting-an-error/108432?utm_source=chatgpt.com)).  
- One shared frustration: “The payment still would not go through. My card works perfectly everywhere else, just not here” ([forum.cursor.com](https://forum.cursor.com/t/cursor-wont-take-my-money/19406?utm_source=chatgpt.com)).

**Warning**: Cursor’s payment system is a minefield. If you're outside the U.S., or even within it, be prepared for endless decline messages, no refund support, and few functional payment options.

## Claude Code: Phone-number maze and temp-roadblocks

- Claude Code forces phone verification—but many hit a roadblock named **“phone_number_temp_blocked.”** Users on Reddit report being blocked mid-onboarding despite multiple attempts ([reddit.com](https://www.reddit.com/r/ClaudeAI/comments/1h1q0ya/cant_get_through_onboarding_phone_number_temp/?utm_source=chatgpt.com)).  

![Claude AI phone verification error](/img/claude.jpg)

- Some report that logging off and trying again later or using a different device worked—pure luck ([reddit.com](https://www.reddit.com/r/ClaudeAI/comments/1h1q0ya/cant_get_through_onboarding_phone_number_temp/?utm_source=chatgpt.com)).  
- Workarounds include using virtual phone numbers or signing up via Google instead of SMS—but these aren’t official solutions, just band-aids ([anakin.ai](https://anakin.ai/blog/error-sending-code-double-check-your-phone-number/?utm_source=chatgpt.com), [calilio.com](https://www.calilio.com/blogs/how-to-use-claude-ai-without-a-phone-number?utm_source=chatgpt.com)).

**Warning**: Claude’s phone system is a gateway straight to frustration. You may never see that verification code—be ready with virtual numbers or VPNs if you're not in a supported region.

## GitHub Copilot: Quota nightmare—even with paid plans

- I hit a wall: “You have exceeded your premium request allowance…” even on a paid account.  

![GitHub Copilot quota exceeded error](/img/copilot.png)

- Turns out the system is by design. Copilot Pro+ gives a specific number of premium-model requests per billing cycle. Once that’s used—bam—you’re downgraded to GPT-4.1 ([github.com](https://github.com/orgs/community/discussions/162585?utm_source=chatgpt.com)).  
- Some users reported hitting the limit immediately after purchase—or before using it at all! ([github.com](https://github.com/orgs/community/discussions/164613?utm_source=chatgpt.com)). Students on educational plans had the same issue: no usage, yet flagged as exceeded ([github.com](https://github.com/orgs/community/discussions/165869?utm_source=chatgpt.com)).  
- It seems the quotas reset only according to billing cycles—not the calendar month—adding to the confusion ([github.com](https://github.com/orgs/community/discussions/164643?utm_source=chatgpt.com)).  
- The system now shows multipliers: e.g., GPT-4.5 consumes **50×** a single premium request; Claude Opus 4 uses **10×**. Expect your budget to vanish fast ([github.com](https://github.com/orgs/community/discussions/165480?utm_source=chatgpt.com)).  
- Users are crying foul: “I only used 13 requests… still can’t get more without paying extra” ([reddit.com](https://www.reddit.com/r/GithubCopilot/comments/1lell0w/you_have_exceeded_your_premium_request_allowance/?utm_source=chatgpt.com)).

**Warning**: Even if you're *paying*, Copilot often denies premium access mid-project. Quotas aren’t obvious in advance, and resetting them might take weeks. You may get downgraded mid-code.


## Windsurf
Im not sure if this is specific to this product, but im constantly getting 
```
Resource exhausted: Encountered retryable error from model provider: Global rate limit reached for this model due to high demand. Your request was not processed and your credits have been refunded. Please upgrade to a pro account for priority access or try again in a few minutes.: Rate limit error
```
![GitHub Copilot quota exceeded error](/img/windsurf.png)

---

### TL;DR – Friendly Warnings for the Unprepared

| Tool            | Common Pitfall                                            | Real-World Reports                           |
|-----------------|------------------------------------------------------------|-----------------------------------------------|
| **Cursor AI**    | Payment failures, limited options, no refunds             | Global users stuck with declined cards ([forum.cursor.com](https://forum.cursor.com/t/cursor-wont-take-my-money/19406?page=2&utm_source=chatgpt.com)) |
| **Claude Code**  | Razor-tight phone verification, vague blocks             | Onboarding blocked by “phone_number_temp_blocked” ([reddit.com](https://www.reddit.com/r/ClaudeAI/comments/1h1q0ya/cant_get_through_onboarding_phone_number_temp/?utm_source=chatgpt.com), [anakin.ai](https://anakin.ai/blog/error-sending-code-double-check-your-phone-number/?utm_source=chatgpt.com)) |
| **GitHub Copilot** | Hidden quota limits—even paid can't use premium models | Quotas hit immediately; students affected ([github.com](https://github.com/orgs/community/discussions/162585?utm_source=chatgpt.com), [reddit.com](https://www.reddit.com/r/GithubCopilot/comments/1lell0w/you_have_exceeded_your_premium_request_allowance/?utm_source=chatgpt.com)) |

---

### My Advice to Future Users

- **Cursor**: Have backup payment methods (virtual cards, international-friendly) ready. Proceed only if your billing system plays nice globally.
- **Claude**: Use virtual numbers or Google sign-in as a backup; test onboarding early before committing.
- **Copilot**: Track your usage from day one. Understand hidden quotas and be prepared for fallback. Budget extras or avoid using premium-heavy features mid-project.

---

Hope this helps anyone venturing into AI-powered coding—be wary. It's a vibe, but in the worst possible way.
