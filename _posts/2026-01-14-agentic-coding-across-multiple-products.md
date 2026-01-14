---
layout: post
title: "How Do You Make AI Understand Business Rules Across Products — And Still Dive Into the Code?"
date: 2026-01-14
categories: [AI, Engineering, Architecture]
tags: [LLM, MCP, Amazon Q, Claude, RAG, Knowledge Systems]
---

## The Question That Actually Started This

> **“How can I make the AI understand our business rules across *all* products —  
> while still being able to dive down to exact code-level details when needed?”**

Not *one* product.  
Not *one* repo.  
Not *one* stack.

Multiple long-lived products.  
Multiple repositories per product.  
Different frameworks. Different versions.  
Shared business logic with subtle differences.

If you’ve ever been responsible for more than one real system, this question probably feels familiar.

---

## Why This Is Harder Than It Sounds

At a business level, things are usually clear:

- “Resellers with 50% commission can’t offer Package A”
- “This only applies in Country B”
- “Product A and B follow the same rule — mostly”

At a technical level, reality looks like this:

- Product A: React + Backend 1
- Product B: Vue + Backend 2
- Pricing logic spread across:
  - frontend conditionals
  - backend validation
  - configs
  - migrations
- Business rules described… *somewhere* in Markdown

The hard part isn’t the rule.  
The hard part is **bridging intent → implementation** across systems.

That’s what I wanted the AI to help with.

---

## The First Attempt: “Just Load the Context”

Like many teams, we leaned into Markdown:

- Business rules written in `.md`
- Glossaries explaining internal terms
- Copilot tools (Amazon Q, later Open WebUI) pointed at them

And at first… it worked.

Until it didn’t.

As products grew, so did the Markdown:
- Per-product summaries
- Shared rules duplicated
- Exceptions layered on

Eventually every question started failing the same way:

> **“Too much context loaded”**

---

## When Bigger Models Still Hit the Wall

At this point, the natural response was:

> “Okay, let’s try the biggest LLM we can.”

We tried **Claude Sonnet 4.5** — massive context window, best-in-class reasoning.

And it helped… briefly.

Then the same pattern emerged:

- Load more Markdown → worse answers
- Load *all* knowledge → hallucinations
- Remove some docs → missing rules
- Trust erodes

This is the moment many teams quietly arrive at the same thought:

> *“Maybe we need to fine-tune.”*

---

## The Fine-Tuning Temptation (and Why It’s a Trap)

Fine-tuning sounds like the ultimate solution:
- “Teach the model our rules”
- “Bake in company knowledge”
- “No more context juggling”

But that idea rests on a false assumption:

> **That the problem is memory.**

It isn’t.

Fine-tuning:
- Changes *behavior*, not structure
- Compresses knowledge, destroying traceability
- Makes rules harder to audit
- Requires retraining as rules change

You don’t want the model to *remember* your business logic.

You want it to **consult it correctly**.

---

## The Realization: Humans Don’t Load Everything Either

The turning point was embarrassingly simple:

> **Senior engineers don’t load the entire system before fixing something.**

They:
1. Clarify intent
2. Consult business rules
3. Identify scope
4. Navigate code *selectively*

We were forcing LLMs to skip steps 1–3 and jump straight to step 4 — with all knowledge shoved into a single prompt.

That’s not intelligence.
That’s overload.

---

## Markdown Was Never the Problem

This part surprised me:

**Markdown was actually the best part of the system.**

During meetings:
- Someone adds a constraint
- A paragraph gets appended
- The rule is updated immediately

No schemas.
No pipelines.
No re-indexing.

The problem wasn’t Markdown.

The problem was treating Markdown as **always-loaded memory**, instead of **consulted intent**.

---

## The Architectural Shift: From Memory to Consultation

Instead of asking:

> “How do I get the AI to read everything?”

The better question became:

> **“How does the AI ask the *right questions*?”**

That’s where **Model Context Protocol (MCP)** fits — not as an AI feature, but as an architectural boundary.

---

## What MCP Enabled That RAG Couldn’t

With MCP, the AI no longer gets documents dumped into its prompt.

Instead, it gets tools like:

- “Search relevant business rules”
- “Explain this internal term”
- “What domains might this rule affect?”

The AI:
- Starts with intent
- Pulls *only* relevant excerpts
- Decides what code to inspect next

Exactly how an experienced engineer works.

---

## Why This Scales Across Products

Now:
- One business rule lives in one place
- Product differences are contextual, not duplicated
- Markdown stays human-first
- Indexes are optional accelerators
- Context windows stay small and meaningful

The same MCP server can be used by:
- Amazon Q Developer (inside VS Code)
- Open WebUI
- Future agentic tooling

One knowledge system.
Multiple products.
Multiple entry points.

---

## The Outcome

What started as a simple question:

> “How can I make AI understand business rules across products *and* dive into code?”

Ended with a realization:

> **LLMs don’t fail because they lack knowledge.  
> They fail because we architect them like databases.**

Once we stopped forcing memory  
and started enabling consultation,

the tools finally behaved like senior engineers instead of overconfident interns.

---

## If This Feels Familiar

If you’ve:
- Hit “too much context loaded”
- Tried the biggest available model
- Doubted RAG
- Considered fine-tuning out of frustration
- Worked on more than one real product

This problem isn’t unique to you.

And the solution isn’t a bigger model.

It’s better boundaries.
