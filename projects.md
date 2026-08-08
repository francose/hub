---
layout: page
title: Projects
permalink: /projects/
---

Upstream contributions and my own tools. State is accurate as of 2026-08-08 —
merged means merged, open means still open.

## Upstream, merged

**[microsoft/PyRIT #1704](https://github.com/microsoft/PyRIT/pull/1704)** —
RegexScorer and CredentialLeakScorer. Regex-based secret detection as a first
class scorer, so credential leakage in model output is measurable instead of
anecdotal.

**[microsoft/PyRIT #1774](https://github.com/microsoft/PyRIT/pull/1774)** —
PromptInjectionScorer for OWASP LLM01. Detection scoring for prompt injection
in the same harness.

**[microsoft/PyRIT #1957](https://github.com/microsoft/PyRIT/pull/1957)** —
`safe_extract_zip` helper for remote dataset loaders. Path traversal in archive
extraction, which is an old bug class that keeps reappearing wherever a
framework fetches datasets over the network.

**[google/osv-scanner #2837](https://github.com/google/osv-scanner/pull/2837)** —
Skip version comparison instead of panicking on unsupported ecosystems. A scanner
that crashes on an unrecognized ecosystem fails open in practice, because people
stop running it.

## Upstream, open

**[NVIDIA/garak #1757](https://github.com/NVIDIA/garak/pull/1757)** — Probe for
prompt injection delivered via hidden text in PDFs. Document-borne injection
against pipelines that ingest files.

**[microsoft/PyRIT #2277](https://github.com/microsoft/PyRIT/pull/2277)** —
Best-of-N jailbreak attack plus a CharNoiseConverter. Empirically validated
rather than ported from the paper on faith.

**[google/osv-scalibr #2244](https://github.com/google/osv-scalibr/pull/2244)** —
Block external plaintext HTTP Maven registries by default in the datasource
client.

**[google/sec-gemini #114](https://github.com/google/sec-gemini/pull/114)** —
Adversarial red-team evaluation notebook.

**[anthropics/claude-cookbooks #623](https://github.com/anthropics/claude-cookbooks/pull/623)**
and **[#631](https://github.com/anthropics/claude-cookbooks/pull/631)** —
Multimodal prompt injection defense cookbook, and an LLM output security
scanner.

**[anthropics/claude-code-security-review #104](https://github.com/anthropics/claude-code-security-review/pull/104)**
— Deserialization typo and exclusion list fix.

## Mine

**[guardrails-owasp-llm02](https://github.com/francose/guardrails-owasp-llm02)** —
Guardrails AI validator for OWASP LLM02. Catches credential leaks, XSS, SQLi,
shell commands, and path traversal in model output.

**[bidi_poc](https://github.com/francose/bidi_poc)** — Encoding and Unicode
attack vectors. Study guide, runnable PoCs, defender scanner.

**[sms_gateway_poc](https://github.com/francose/sms_gateway_poc)** — Carrier
email-to-SMS gateways. SMTP send, direct-to-MX, multi-carrier fanout, header
spoofing, plus a defender-side scanner. Authorized testing only.

**[wifi_sense](https://github.com/francose/wifi_sense)** — Passive presence and
motion detection from WiFi signal analysis. Monitor-mode adapter, nothing else.

**[zig_pdf_fuzzer](https://github.com/francose/zig_pdf_fuzzer)** — PDF parser
fuzzing in Zig.

**[jynx-pi](https://github.com/francose/jynx-pi)** — Go networking exercise.
TCP/UDP scanner, HTTP proxy, request repeater, banner-based vuln check, ICMP
discovery.

**[windows_malware](https://github.com/francose/windows_malware)** — Living
off the land: PowerShell reverse shell staged over HTTP, back to a netcat
listener. Lab use.

**[jinx_web_framework](https://github.com/francose/jinx_web_framework)** —
Express-inspired web framework for Go.

**[myotis-theme](https://github.com/francose/myotis-theme)** — Gruvbox Dark for
GNOME Terminal plus a git-aware zsh prompt, one-command install.
