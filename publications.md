---
layout: page
title: Publications
permalink: /publications/
---

ORCID: [0009-0006-9637-6345](https://orcid.org/0009-0006-9637-6345)

## Out-of-Bounds Memory Access in the Zig Programming Language

An Empirical Study of CWE-787 and CWE-125 Across Build Modes. Preprint, July
2026. [doi:10.5281/zenodo.21347038](https://doi.org/10.5281/zenodo.21347038)

Zig keeps C-style manual memory management and adds runtime bounds checks, but
only in Debug and ReleaseSafe. ReleaseFast and ReleaseSmall drop them. I wrote
four small programs (an out-of-bounds read that leaks a secret, a write that
corrupts an adjacent flag, a write that hijacks control flow, and a many-item
pointer read) and ran each under all four modes.

The same source panics safely in the checked modes and becomes a working
exploit primitive in the unchecked ones. Nothing changes but the optimisation
flag.

The fourth program is the one worth reading. It leaks in every mode, including
the safe ones, because a many-item pointer carries no length for the check to
test against. The safety you get is a property of the type, not of the build
mode you picked.
