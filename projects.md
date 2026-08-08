---
layout: page
title: Projects
permalink: /projects/
---

## ThinkEngine

AI security platform — agent orchestrator, remediation pipeline, multi-tenant
isolation. Deployed and running. Lead and main contributor; it's being open
sourced at Comcast.

[docs](https://github.com/thinkengineio/docs)

## sentinel-maas

Endpoint security agent, written in Go from scratch, cross-platform on macOS
and Linux. Collects system and network telemetry, does CIS hardening checks,
file integrity monitoring, vulnerability and threat detection, package and
service inventory, and credential scanning. Ships remediation actions rather
than only alerting.

The part I like most is ATTM — an adversarial testing module built into the
agent. It generates synthetic events mapped to 19 MITRE ATT&CK techniques and
sends them through the real ingestion path, so you find out whether your
detection and alerting actually fire instead of assuming they do. Everything it
emits is tagged synthetic so it can't contaminate real findings.

Currently closed source.

## Sleuthgraph

Mine. Open-source OSINT investigation workbench, graph-native, organised around
pivots rather than search results.

- [sleuthgraph](https://github.com/thinkengineio/sleuthgraph) — meta repo, docs, compose
- [sleuthgraph-api](https://github.com/thinkengineio/sleuthgraph-api) — FastAPI, Postgres + AGE, plugin SDK
- [sleuthgraph-web](https://github.com/thinkengineio/sleuthgraph-web) — Next.js, Cytoscape.js

## Upstream, merged

**[PyRIT #1704](https://github.com/microsoft/PyRIT/pull/1704)** — RegexScorer
and CredentialLeakScorer. Makes credential leakage in model output measurable
instead of anecdotal.

**[PyRIT #1774](https://github.com/microsoft/PyRIT/pull/1774)** —
PromptInjectionScorer for OWASP LLM01.

**[PyRIT #1957](https://github.com/microsoft/PyRIT/pull/1957)** —
`safe_extract_zip` for remote dataset loaders. Path traversal in archive
extraction, an old bug class that reappears every time a framework starts
fetching datasets over the network.

**[osv-scanner #2837](https://github.com/google/osv-scanner/pull/2837)** — Skip
version comparison instead of panicking on unsupported ecosystems. A scanner
that crashes on an unrecognised ecosystem fails open in practice, because
people stop running it.

## Upstream, open

**[garak #1757](https://github.com/NVIDIA/garak/pull/1757)** — Probe for prompt
injection hidden in PDF text. Document-borne injection against pipelines that
ingest files.

**[PyRIT #2277](https://github.com/microsoft/PyRIT/pull/2277)** — Best-of-N
jailbreak plus a CharNoiseConverter. Ran the ASR sweep myself rather than
trusting the paper's numbers.

**[osv-scalibr #2244](https://github.com/google/osv-scalibr/pull/2244)** —
Block external plaintext HTTP Maven registries by default.

**[sec-gemini #114](https://github.com/google/sec-gemini/pull/114)** —
Adversarial red-team evaluation notebook.

**[claude-cookbooks #623](https://github.com/anthropics/claude-cookbooks/pull/623)**
and **[#631](https://github.com/anthropics/claude-cookbooks/pull/631)** —
Multimodal prompt injection defense, and an LLM output security scanner.

**[claude-code-security-review #104](https://github.com/anthropics/claude-code-security-review/pull/104)**
— Deserialization typo and exclusion list fix.

## Mine

**[guardrails-owasp-llm02](https://github.com/francose/guardrails-owasp-llm02)**
— Guardrails AI validator for OWASP LLM02. Credential leaks, XSS, SQLi, shell
commands, path traversal in model output.

**[bidi_poc](https://github.com/francose/bidi_poc)** — Encoding and Unicode
attacks. Runnable PoCs plus a defender scanner.

**[sms_gateway_poc](https://github.com/francose/sms_gateway_poc)** — Carrier
email-to-SMS gateways. SMTP send, direct-to-MX, multi-carrier fanout, header
spoofing, defender scanner. Authorized testing only.

**[wifi_sense](https://github.com/francose/wifi_sense)** — Presence and motion
detection from WiFi signal analysis. Monitor-mode adapter, nothing else.

**[zig_pdf_fuzzer](https://github.com/francose/zig_pdf_fuzzer)** — PDF parser
fuzzing in Zig.

**[jynx-pi](https://github.com/francose/jynx-pi)** — Go networking exercise.
TCP/UDP scanner, HTTP proxy, request repeater, banner check, ICMP discovery.

**[windows_malware](https://github.com/francose/windows_malware)** — Living off
the land. PowerShell reverse shell staged over HTTP to a netcat listener. Lab
use.

**[jinx_web_framework](https://github.com/francose/jinx_web_framework)** —
Express-inspired web framework for Go.
