---
layout: page
title: Toolkit
permalink: /toolkit/
---

What I actually reach for, grouped by the job rather than the vendor. Tools
churn; the jobs don't.

## Adversarial ML evaluation

PyRIT for scored, repeatable attack runs — the scoring harness is the part that
matters, since an attack you can't measure is a demo. garak for probe-based
scanning across a model surface. Both are worth reading as codebases, not just
running.

## Application and infrastructure testing

Burp for anything HTTP. Nmap for discovery. Standard Kali toolchain for the
rest. Nothing exotic — the constraint is almost always methodology, not
capability.

## Supply chain

OSV-Scanner for dependency vulnerabilities, SCALIBR when I need the inventory
rather than the verdict.

## OSINT collection

Search operators first, and they stay the highest return per minute. Archive and
cache retrieval for surfaces that have since locked down. Reverse image search
and geolocation corroboration through mapping and street-level imagery — that
last one is chronically underrated and is where the high-value findings actually
come from.

Everything read-only. Anything that authenticates, probes, or leaves a
notification is out of scope for passive collection.

## Detection and rules

Sigma for portable detection logic. Reading ATT&CK as a coverage map rather
than a checklist.

## Writing and reproducibility

Everything gets a repro path — command, query, or source URL — attached at the
moment I find it, not reconstructed later. A finding I can't re-evidence is a
finding I can't defend.
