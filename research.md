---
layout: page
title: Research
permalink: /research/
---

What I'm working on. Each area has something public attached to it — a
contribution, a tool, or a paper.

## Adversarial evaluation of LLM and agentic systems

Most of my time. Injection that survives a real pipeline, scoring harnesses
that measure it, and jailbreak techniques worth putting into a framework other
people run.

The work so far: prompt injection and credential-leak scoring in PyRIT, a
Best-of-N jailbreak implementation, a PDF injection probe for garak, an OWASP
LLM02 output validator.

One thing I keep coming back to. Most published injection results are measured
against a bare model. The number I want is how much of it still works with a
retrieval pipeline, a system prompt, a tool allowlist, and an output filter in
the way. That gap is the actual risk estimate, and almost nobody reports it.

## Agent containment and egress

An agent that can reach the network makes most other controls advisory.
Capability scoping, token exchange, and treating egress as the control plane
rather than sandboxing as a checkbox.

## Supply chain

Contributions to Google's OSV tooling. A crash fix in the scanner's version
comparison, and blocking plaintext HTTP Maven registries by default in
SCALIBR's datasource client.

## Encoding and parser attack surface

Unicode attacks that get through review because the text looks fine — Trojan
Source, homographs, overlong UTF-8, null-byte truncation. PoCs and a defender
scanner in [bidi_poc](https://github.com/francose/bidi_poc).

Related: memory safety across build modes in languages that claim to give it to
you for free. Wrote that one up — see
[publications]({{ '/publications/' | relative_url }}).

## Messaging identity

Sender verification and impersonation surface in carrier and business
messaging. Email-to-SMS gateways, RCS, iMessage business channels. PoCs and a
defender scanner in
[sms_gateway_poc](https://github.com/francose/sms_gateway_poc).

## Passive RF sensing

Presence and motion detection off WiFi signal analysis. No cameras, no
wearables. I care about it as a privacy surface — commodity hardware that
senses through walls is a collection capability whether anyone called it that
or not.

## OSINT tradecraft

Passive collection, pivot chains, and documenting a finding so someone else can
audit the reasoning. Also the product side of it —
[Sleuthgraph]({{ '/projects/' | relative_url }}) is an open-source
investigation workbench.
