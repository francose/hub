---
layout: page
title: Publications
permalink: /publications/
---

ORCID: [0009-0006-9637-6345](https://orcid.org/0009-0006-9637-6345)

## Determinism is not soundness

An empirical evaluation of seccomp argument-inspection gates for containing LLM
agents. With Todd Outten. Preprint, September 2026.
[doi:10.5281/zenodo.23070684](https://doi.org/10.5281/zenodo.23070684)

Sandboxes for agent code keep reaching for a seccomp supervisor that reads each
syscall's arguments and allows or denies the call. The verdict is deterministic,
and it is easy to take that for containment. We tested whether it holds,
reading behaviour from the kernel instead of from what the model said.

On its own it doesn't. The gate blocks a forbidden write 4000 times out of 4000
head-on. Swap the path after the supervisor reads it and before the kernel
does, and the write gets through on 18.9 to 23.1 percent of attempts. An agent
that retries only has to win once.

What held was moving the decision into the kernel, where it acts on the real
object: a Landlock allowlist, an exec deny, a network namespace with no route
out, and a cgroup kill switch. That stack stopped eight of the nine attacks we
ran. The ninth wrote through a file handle opened before the sandbox was set
up, which is the launcher's job to strip, not the kernel's. Order mattered too.
Killing the agent after detection still let the operation it was caught on
complete. Denying first and then killing kept it clean.

This is evidence from one host against the attacks we ran, not a proof.
Interfaces we didn't test and kernel or hardware bugs are out of scope.

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
