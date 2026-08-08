---
layout: post
title: "Scoring model output instead of trusting it"
date: 2026-05-20 20:00:00 -0400
tags: [ai-security, owasp, guardrails]
---

OWASP LLM02 covers insecure output handling: the model emits something, your
application does something with it, and the something turns out to be a shell
command. The category is well described and the tooling for actually measuring
it was thin, so I wrote a
[Guardrails validator](https://github.com/francose/guardrails-owasp-llm02) for
it.

Five categories, all detected on the output side rather than the prompt side:
credential leaks, XSS, SQL injection, shell commands, and path traversal.
Compiled regex, ErrorSpan support so a caller knows which span tripped, and a
`fix_value` redaction path so it can sanitise rather than only reject.

The design decision worth stating is that this scores output, not input.
Prompt-side filtering gets most of the attention because it feels like
prevention, but it's guessing at intent. Output-side detection is looking at an
artifact that either does or doesn't contain an AWS key. Much less ambiguous,
and it catches the cases where the model produced something dangerous with no
adversarial prompt involved at all, which in practice is a lot of them.

The same reasoning went into the PyRIT scorers I contributed around the same
time. Detection you can point at a concrete string is detection you can
regression-test.

What I'm not claiming: regex is a floor, not a ceiling. It catches shaped
secrets like AWS keys and GitHub tokens, and it will miss anything that doesn't
have a shape. Selective scanner config and an `extra_patterns` hook are in
there because the default set is a starting point that everyone will need to
extend for their own environment.
