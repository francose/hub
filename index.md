---
layout: page
---

I'm a security engineer at Comcast, and most of what I do outside that is
adversarial evaluation of LLM and agentic systems. Prompt injection that
survives a real pipeline instead of a bare model, scoring harnesses that catch
it, and what containment actually buys you once an agent can reach the network.

That work lands upstream where it can. Prompt injection and credential-leak
scoring in PyRIT, a Best-of-N jailbreak implementation, a PDF injection probe
for garak, crash and hardening fixes in Google's OSV tooling. I'd rather put a
technique into a framework other people already run than publish a tool nobody
installs.

The rest of my time goes to building. sentinel-maas is an endpoint agent I
wrote from scratch in Go, with an adversarial testing module that fires
synthetic ATT&CK techniques through the real detection path to prove the
pipeline works. Sleuthgraph is an open-source OSINT investigation workbench,
graph-native and built around pivots. I lead ThinkEngine, which is being open
sourced at Comcast.

I do CTFs — OSINT search parties, and I'm working through OSCP material — and I
run bug bounty on the side. Both are the same appeal: a scoreboard is an
unusually honest referee. You either found the thing or you didn't, and nobody
grades your methodology section.

Formally: MSE in Software Systems and Cybersecurity from Penn, CISSP, CEH,
Security+, CCNA. One [preprint]({{ '/publications/' | relative_url }}) so far,
on out-of-bounds memory access in Zig across build modes.

This site is the working notes. Research is what I'm on now, projects is what
I've shipped, notes are short findings — what I tried, what held, what didn't,
and what I'm not claiming.

Public work only. Nothing from the day job, nothing under review.
