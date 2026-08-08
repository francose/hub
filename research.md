---
layout: page
title: Research
permalink: /research/
---

Areas I'm actively working. Each one is anchored to something public — a
contribution, a tool, or a writeup. If there's no artifact next to it, it isn't
listed yet.

## Adversarial evaluation of LLM and agentic systems

The bulk of it. Prompt injection that survives a real pipeline rather than a
demo, scoring harnesses that detect it, and jailbreak techniques worth
implementing in a framework other people run.

Concretely: prompt injection scoring and credential-leak detection in
[PyRIT]({{ '/projects/' | relative_url }}), a Best-of-N jailbreak attack
implementation, a PDF injection probe for garak, and an OWASP LLM02 output
validator.

Open question I keep circling: most published injection results are measured
against a bare model. The interesting number is how much of it survives a
retrieval pipeline with a system prompt, a tool allowlist, and an output filter
in front of it. That gap is where the real risk estimate lives.

## Agent containment and egress

If an agent can reach the network, most other controls are advisory. Interested
in capability scoping, token exchange patterns, and egress as the actual
control plane rather than sandboxing as a checkbox.

Determinism in a sandbox is not the same property as soundness, and the two get
conflated constantly.

## Supply chain and dependency security

Contributions to Google's OSV tooling — a crash fix in the scanner's version
comparison, and blocking external plaintext HTTP registries by default in
SCALIBR's Maven client. Small changes, but the failure modes are the kind that
matter at scale.

## Encoding and parser attack surface

Unicode and encoding attacks that slip past review because the text looks
normal — Trojan Source, homograph substitution, overlong UTF-8, null-byte
truncation. Runnable PoCs plus a defender-side scanner in
[bidi_poc](https://github.com/francose/bidi_poc).

Adjacent: fuzzing PDF parsers, and memory safety failure modes across build
modes in languages that claim to prevent them.

## Messaging identity

Sender verification and impersonation surface in carrier and business messaging
— email-to-SMS gateways, RCS and iMessage business channels. Study material and
PoCs in [sms_gateway_poc](https://github.com/francose/sms_gateway_poc),
defender scanner included.

## RF and passive sensing

Presence and motion detection from WiFi signal analysis, no cameras and no
wearables. Interesting to me as a privacy surface more than as a product —
commodity hardware that senses through walls is a collection capability whether
or not anyone framed it that way.

## OSINT tradecraft

Passive collection methodology, pivot chains, and the discipline of documenting
a finding so the reasoning is auditable. See the [notes]({{ '/notes/' | relative_url }}).
