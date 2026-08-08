---
layout: post
title: "Encoding attacks survive review because the text looks fine"
date: 2026-05-18 19:00:00 -0400
tags: [opsec, encoding, appsec]
---

Most classes of bug get caught because someone reads the diff and something
looks off. Encoding attacks are the exception. The reviewer reads exactly what
the attacker wants them to read, and the compiler reads something else.

I put together runnable PoCs for the ones that keep working:
[bidi_poc](https://github.com/francose/bidi_poc). Trojan Source, where
bidirectional control characters reorder how source displays without changing
what parses. Homograph substitution, where a Cyrillic character stands in for a
Latin one. Overlong UTF-8, where a byte sequence decodes to something a naive
validator already approved in its shorter form. Null-byte truncation, where the
parser and the validator disagree about where the string ends. Double encoding,
which beats any filter that decodes once.

The pattern underneath all of them is the same: two components in the pipeline
disagree about what a byte sequence means, and the security decision gets made
by the one with the more permissive reading.

There's a defender scanner in the repo too, which is the part I'd actually use.
Detection here is mechanical. You're looking for byte patterns, not intent, so
it belongs in CI rather than in a reviewer's head. Asking humans to spot a
right-to-left override in a code review is asking them to do something the
rendering layer is actively working against.

What I'm not claiming: none of these are novel, and Trojan Source in particular
got a CVE and a lot of attention in 2021. The finding, if there is one, is that
they still land. The techniques are old and the pipelines that mishandle them
are new every year, because every new parser gets to rediscover the same
disagreement.
