---
layout: post
title: "Spec Debt Compounds Faster Than Tech Debt"
subtitle: "You don't have a documentation problem. You have an architecture problem."
date: 2026-05-02
author: Gautam Bandyopadhyay
---



---

Every product team I've worked with talks about technical debt obsessively. It has a name, a measurement, a quarterly slot in the engineering review. Almost no one talks about spec debt — yet in the lending businesses I've spent the last two decades inside, spec debt is what actually slows things down.

It compounds faster than tech debt. And almost nobody is tracking it.

---

> **TL;DR**
>
> Most enterprise documentation is organised around *when* it was written, not *what* it describes. A file titled `BRD_Lending_Origination_Eligibility_Disbursement_v3_2.docx` isn't one document — it's fragments of three or four user stories that landed on the same author's desk on the same day.
>
> The cost is invisible but corrosive. Every reviewer pays a *reconciliation tax* — mentally diffing five descriptions of the same flow scattered across product variants, change requests and reviewer-comment threads — to figure out what the system actually does.
>
> The fix isn't to write more. It's to *re-architect*: choose a namespace that mirrors the system, promote cross-cutting flows to common, and re-project existing content onto the new spine. AI makes this work an order of magnitude cheaper than it used to be — which means the only excuse left is that nobody noticed it was possible.

---

![The system has one shape. The documents have another.](/assets/article_1_infographic.png)


## The signal you're already in spec debt

Look at your documentation folder. Find a filename with three or more nouns separated by underscores.

Something like:

> `BRD_Lending_Origination_Eligibility_Disbursement_v3_2.docx`

If you have one, you have spec debt.

That filename isn't describing a single coherent thing. It's a record of what one author thought about during one authoring session. Three different concerns ended up in the same document because they were all on someone's mind that week. Three different reviewers commented on three different sections. Some sections got signed off; others didn't. Three years later, a new joiner trying to understand "how does our KYC flow work for renewal customers" has to know to open *this* file, scroll to the right section, ignore two other sections, cross-reference two other multi-noun files written by different authors, and mentally diff their differences.

That's the moment the cost of spec debt shows up.

---

## Documents describe authoring sessions, not user stories

This is the architectural failure most teams don't see.

The system you're building has a structure: user stories, modules, products, flows. The documents you've inherited do not match that structure. They match the calendar. Each captures what was on the desk that week, not what belongs together logically. Over years, you accumulate dozens — each one a snapshot of a moment, none of them a canonical description of anything in particular.

Walk through a typical lending product spec corpus and you'll see this shape:

- Five different documents describe how mobile number capture and OTP verification work — one for each product flavour. The flows are 90% identical, but each was written separately at a different point in time. None references the others.
- A change request from 18 months ago modified address-parsing rules. It touched three downstream user stories. The change document exists; the original specs were never updated. Today the original specs and the change document disagree, and nobody knows which is "live."
- A reviewer comment asked for a field to be removed from scope. Someone replied "agreed." Six months later the field is still in the spec because nobody actioned the comment.
- The company rebranded two years ago. Half the documents still carry the old name. Search-and-replace was never run.

Each is small in isolation. Together they are why new product decisions take three weeks of "let me get back to you" instead of three days.

---

## The reconciliation tax

Every reader of these documents pays the same hidden tax.

To answer a single question — *"what does mobile capture do for an existing-to-bank renewal customer?"* — a reviewer must find the right product-flavour document among five, check the change-request log for amendments, open the comments thread for shifted designs, then mentally diff all three sources to arrive at the *current* truth. Each step takes two minutes. Across a full PRD review, an architect pays this tax dozens of times. Across a quarter, an organisation pays it thousands.

It does not show up on any P&L. There's no line item called "time spent reconciling documents that should have been one document." But it determines build velocity, decision quality, and whether a product team that *looks* like it should ship in six weeks actually ships in six months.

---

## Why writing more documents won't fix it

The natural reaction, when documentation feels broken, is to write more of it. Better templates. Mandatory sections. Stricter review processes.

This makes the problem worse.

Spec debt is not a content problem. The information you need is almost always already written down somewhere. What's missing is the *spine* — the architectural layer that says "here are the user stories, here are the cross-cutting flows, here is the canonical place to describe each thing, here is how changes flow through the structure."

Without a spine, every new document creates more reconciliation surface. With a spine, new content slots into a known place, and old content can be retired explicitly.

You don't have a documentation problem. You have an information architecture problem.

---

## What a spine looks like

The fix has three components, none of them exotic:

**A namespace that mirrors the system, not the calendar.** Every use case gets a stable identifier — `<solution>-<product>-<module>-<userstory>-<sequence>` — and every piece of content lives under one of those identifiers. Documents become *views* into the namespace. The namespace is the source of truth.

**Cross-cutting use cases promoted to common.** Flows that appear in three or more user stories — OTP verification, digital consent, dedupe orchestration — are pulled into a common namespace and referenced everywhere they're used. One canonical description, many references. The day someone changes the OTP flow, they change it once.

**Inline product deltas instead of per-product documents.** When the same flow exists across five product flavours with small differences, do not write five documents. Write one description with explicit per-product deltas inline — "for renewals, this field is auto-populated from the previous loan cycle and non-editable; otherwise user-input." Reviewers see all five products at once and the redundancy disappears.

These three moves alone shrunk a 100-page per-product spec down to 36 pages covering five product flavours — not because the information shrunk, but because the redundancy did.

---

## The carve-out: a workflow

Re-architecting documentation you've inherited is a tractable activity, not a heroic one. The workflow is four steps.

**1. Inventory without judgement.** List every source — documents, change requests, reviewer comment threads, design notes. Don't filter yet. The instinct to declare some sources "canonical" before you've seen them all is what got you into this mess.

**2. Find the spine.** What is the smallest set of user stories that, taken together, covers everything in the inventory? Most lending corpuses collapse cleanly into five or six: dashboard, customer onboarding, eligibility, household assessment, account setup, disbursement. The spine is rarely surprising once you stop letting the documents define it.

**3. Project content onto the spine.** For each piece of content in each source, decide which user story it belongs to. This is the judgement-heavy step and the one most teams skip. A document titled `BRD_W_X_Y_Z` will project onto four different user stories. That's expected. The act of carving is the work.

**4. Decide on namespace and deltas.** Once content is projected onto user stories, choose how to handle product variation. Per-product documents are easier to write but high-redundancy. Inline deltas with a shared namespace are easier to review but require more discipline upfront. For most lending shops, deltas win.

That's the entire workflow. Not glamorous, but the highest-leverage thing a product team can do for itself in a quarter — because everything else gets faster afterward.

---

## Why this matters now

For most of the last decade, this work was prohibitively expensive. Carving fifteen disparate documents into a coherent namespace was a six-week effort with three full-time analysts, and no one ever had the budget. So the corpus drifted. Spec debt accumulated. Teams adapted by paying the reconciliation tax in perpetuity.

That has changed.

Re-architecting a documentation corpus is exactly the kind of work AI-augmented workflows handle well: comprehending unstructured text, identifying redundancy, projecting content onto a target structure, generating consistent output. The judgement layer — what belongs together, what's the right namespace, where this delta lives — stays human, and is exactly where senior product knowledge compounds. The mechanical layer moves to the machine.

A six-week effort is now a sitting. The quality is higher because the same operator holds the entire corpus in working memory. Every claim is traceable to a source.

This is not a thought experiment. We've been running this loop on our own documentation at Trustt as part of AiTDP — our internal AI-augmented product development process. AiTDP was originally framed around the downstream pipeline: PRDs flow into Technical Requirement Documents, which flow into code generation, with human-in-the-loop checkpoints at each handoff. What's becoming clear is that the *upstream* piece — the PRD itself, and before it, the architectural reconciliation of years of source material — is the highest-leverage place to apply the same approach. The downstream pipeline only works as well as the spec it consumes. Fix the spec, and everything that follows accelerates.

---

## What to do on Monday

If you've read this far and you're nodding, here is the smallest useful action you can take this week:

Open your team's documentation folder. Sort by filename. Count the files with three or more nouns in the name.

That number is your spec debt index. It will not be zero. It is probably much higher than you'd guess.

That's the diagnosis. The treatment is the workflow above. The good news is the treatment is now an order of magnitude cheaper than it was a year ago.

The only question is whether you start.

---

*This is the first essay in a three-part series on AI-augmented product work in financial services. The next piece — "Documentation Is Structured-Thinking-at-Scale" — looks at why the judgement layer becomes more valuable, not less, when AI handles the drafting. The third piece — "The Compaction Test" — covers how to design AI-augmented workflows so the work survives any single session ending.*
