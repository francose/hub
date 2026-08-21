---
layout: post
title: "Best-of-N jailbreaks: the climb is real, the leaderboard isn't"
date: 2026-07-27 18:00:00 -0400
tags: [ai-security, pyrit, jailbreak]
---

Most jailbreak numbers tell you more about how they were scored than about the
model. Change what counts as a success and the same attack goes from marginal
to devastating without a single prompt changing.

The occasion for this was porting Best-of-N
([Hughes et al., arXiv:2412.03556](https://arxiv.org/abs/2412.03556)) to PyRIT. The maintainer's condition was reasonable: don't port the paper on
faith, show it works. So I ran it against live targets instead of citing the
abstract. But the port was the easy half. The part that took the thinking was
building a measurement I would believe.

Start with the scorer, because everything downstream inherits it. I used the
`task_achieved_refined` true/false scorer, which asks one question: did the
response fulfill the request, or refuse. The obvious alternative is the QA
scorer, which asks whether the answer is correct. That is the wrong question
for a jailbreak, because a well-formed refusal is a correct answer. Score with
it and refusals land on the same side of the ledger as compliance.

Setup was 15 objectives from the bundled scorer-eval sets across illegal
activity, cyber, phishing, harassment and misinformation. Sigma 0.4, one
augmented sample per attempt, loop until something breaks through, record the
first-success index.

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
gaps between models sit inside the noise, so read the numbers as "ASR rises with
N everywhere" and not as a ranking.

One target was simply unreadable: gpt-5.5 rejects at an API-level policy filter
before the model sees the prompt. That's a vendor input classifier, not a model
refusal, and measuring it would answer a different question.

The scaling result itself was never in doubt. Hughes et al. established it at
far more samples than I ran, and my job was to corroborate it, not to discover
it. What I wanted out of the exercise was knowing which parts of an ASR number
are load bearing and which are an artifact of the harness around it. Pick a
scorer that counts refusals as wins, let a target grade its own output, skip
reading the transcripts, and you can put almost any curve on a slide. The
attack was the smaller problem.

[PR #2277](https://github.com/microsoft/PyRIT/pull/2277)
