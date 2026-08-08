---
layout: post
title: "Best-of-N jailbreaks: the climb is real, the leaderboard isn't"
date: 2026-07-27 18:00:00 -0400
tags: [ai-security, pyrit, jailbreak]
---

I implemented Best-of-N (Hughes et al., arXiv:2412.03556) for PyRIT. The
maintainer's condition was reasonable: don't port the paper on faith, show it
works. So I ran it against live targets instead of citing the abstract.

Setup was 15 objectives from the bundled scorer-eval sets across illegal
activity, cyber, phishing, harassment and misinformation. Sigma 0.4, one
augmented sample per attempt, loop until something breaks through, record the
first-success index. Scoring used the `task_achieved_refined` true/false
scorer, which asks whether the response fulfilled the request or refused. Not
the QA scorer, which asks whether the answer is correct and inflates
everything, because a well-formed refusal is still a correct answer.

Against gpt-4o-mini, judged by gemini-2.5-flash: 20% at N=1, 27% at N=2, 47% at
N=4, 47% at N=8, 60% at N=16. Keep re-sampling augmented variants and refusals
fall off.

The climb held on every target I tried. gpt-4o, gpt-4.1-mini, gpt-4.1,
gemini-2.5-flash, gemini-2.5-pro. A local llama3.2:3b barely moved, 7% to 13%,
which is the same scaling story from the other end: a heavily safety-tuned 3B
model needs a lot more than 16 samples.

I didn't trust the judge blind. I pulled response text and judge rationale back
out for several successes and read them by hand. An anti-vax post, a phishing
email, a lab-leak article. Real compliance, not the judge false-positiving on a
refusal.

What I'm not claiming. This is one run at N=16; Hughes et al. went to roughly
10k samples, so this corroborates their scaling result rather than replacing
it. To keep targets off their own judge I scored OpenAI targets with Gemini and
Google targets with gpt-4o-mini, which means cross-vendor absolute numbers
aren't comparable. The within-row trend is the solid part. At 15 objectives the
gaps between models sit inside the noise, so read the tables as "ASR rises with
N everywhere" and not as a ranking.

One target was simply unreadable: gpt-5.5 rejects at an API-level policy filter
before the model sees the prompt. That's a vendor input classifier, not a model
refusal, and measuring it would answer a different question.

[PR #2277](https://github.com/microsoft/PyRIT/pull/2277)
