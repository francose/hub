---
layout: page
title: Toolkit
permalink: /toolkit/
---

Grouped by the job, not the vendor. Tools churn; the jobs don't. A fair amount
of this is stuff I ended up writing because nothing existing did the thing.

## Adversarial ML evaluation

PyRIT for scored, repeatable attack runs, from a dev checkout rather than the
release, since most of what I want to try means touching the scorers. The
scoring harness is the part that matters. An attack you can't measure is a
demo.

garak for probe-based scanning across a model surface, also from a fork,
usually because I'm writing a probe rather than running one.

Guardrails for output validation, plus
[my own LLM02 validator](https://github.com/francose/guardrails-owasp-llm02)
for credential leaks, XSS, SQLi, shell commands and path traversal in model
output.

## Recon and network

nmap for discovery. httpx and nuclei for sweeping web surface at volume.
tcpdump when I need to see what actually went out on the wire rather than what
the application claims it sent.

[jynx-pi](https://github.com/francose/jynx-pi) is my own Go take on the basics:
TCP and UDP scanner, HTTP proxy, request repeater, banner checks, ICMP
discovery. Writing them is how I learned what the real ones are doing.

Kali VM for lab work and exam practice.

## Binaries and memory

gdb, objdump, strace, capstone. Enough to answer why a crash is a crash and
whether it's reachable.

[zig_pdf_fuzzer](https://github.com/francose/zig_pdf_fuzzer) for parser
fuzzing. That line of work turned into the
[Zig out-of-bounds paper]({{ '/publications/' | relative_url }}), which is
mostly an argument that the safety you get is a property of the type, not of
the build mode you picked.

## Supply chain

OSV-Scanner for dependency vulnerabilities, SCALIBR when I want the inventory
rather than the verdict. I've contributed to both, which is largely how I
learned where they break.

## Endpoint and detection

[sentinel-maas]({{ '/projects/' | relative_url }}), my own agent. File
integrity monitoring, CIS hardening checks, vulnerability and threat detection,
credential scanning, and ATTM for firing synthetic ATT&CK techniques through
the real ingestion path to confirm detections actually fire.

Building the detection and building the thing that tries to slip past it in the
same codebase keeps both honest.

## OSINT

[Sleuthgraph]({{ '/projects/' | relative_url }}), also mine. Graph-native
investigation workbench organised around pivots instead of search results.

Beyond that: search operators first, and they stay the highest return per
minute. Archive and cache retrieval for surfaces that have since locked down.
Reverse image search and geolocation corroboration through mapping and
street-level imagery.

Everything read-only. Anything that authenticates, probes, or leaves a
notification is out of scope for passive collection.

## Writing it down

Every finding gets a repro path attached at the moment I find it, a command, a
query, or a source URL, not reconstructed afterwards. A finding I can't
re-evidence is a finding I can't defend.
