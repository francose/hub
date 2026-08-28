---
layout: post
title: "PyRIT from zero: install, first run, and the wire that flips your verdict"
date: 2026-08-27 20:45:00 -0400
tags: [ai-security, pyrit]
---

I ran everything below tonight on a clean virtualenv, against a model on my own
laptop, no API key anywhere in it. The hosted-endpoint bit at the end is the
documented path and I didn't re-run it, so I've said so where it comes up.

## What PyRIT is

PyRIT is Microsoft's open source framework for testing generative AI systems.
The way I'd put it: it's the plumbing between a prompt and a verdict. You hand it
a target, an objective, and some way to score the answer, and it runs the loop
and writes it all to a database you can go back and query.

Worth naming the pieces, because the docs assume you already know them. A
**target** is the thing you're testing, an OpenAI endpoint, a local model, an
HTTP API, a web app driven through Playwright. A **converter** mangles a prompt
before it goes out, base64, ROT13, character noise. A **scorer** decides whether
the response counts as a win. An **attack** wires those together and drives the
conversation. A **scenario** bundles a pile of attacks and datasets so you can
run at scale.

That's it, honestly. Everything else in PyRIT is a variation on those five.

## Why it's worth using

The case for PyRIT isn't clever attacks. Most published attacks are a few dozen
lines and you could write them yourself in an afternoon. The case is that it
makes results comparable.

Rolling your own harness is easy, and that's exactly the trap. I've done it. You
end up with a script that fires prompts, eyeballs the output, and produces a
number nobody else can reproduce, including you three months later. PyRIT hands
you a memory database with every prompt, response and score in it, scorers with
actual names instead of a regex for "I can't help with that", and a target
abstraction where swapping gpt-4o for a local model is one environment variable
rather than a rewrite.

It's also where the work piles up. When I put Best-of-N in, what landed was a
converter anyone can bolt onto an attack that has nothing to do with mine. Beats
a standalone tool nobody installs :)

## Who should bother

If you have to answer "is this model safe enough to ship" and show your work,
this is for you. Same if you're red teaming by hand already and want the results
to survive someone else looking at them. And if you're about to build an eval
harness from scratch, stop, because you're roughly two weeks from rebuilding a
worse version of this.

If you just want to jailbreak a chatbot for fun, this is way too much machinery.
Use a browser haha.

## Install

Python 3.10 through 3.14. Check first, the range has a ceiling and 3.15 isn't in
it.

```bash
python --version
python -m venv .venv && source .venv/bin/activate
pip install pyrit
```

That gets you the latest release off PyPI, 1.0.1 as of tonight. Confirm it,
because the docs on the website track `main` and drift ahead of whatever you
just installed:

```bash
python -c "import pyrit; print(pyrit.__version__)"
```

If that prints something the docs don't seem to expect, go read the docs on the
matching release branch instead:
`https://github.com/microsoft/PyRIT/tree/releases/v1.0.1/doc`. I lost time to
this once and it's an annoying way to lose time.

## Point it at a local model

Cheapest target going is Ollama, which speaks an OpenAI-compatible API. PyRIT
documents this directly. Pull a model, set three variables:

```bash
ollama pull llama3.2:3b

export OPENAI_CHAT_ENDPOINT="http://127.0.0.1:11434/v1"
export OPENAI_CHAT_KEY="not-needed"
export OPENAI_CHAT_MODEL="llama3.2:3b"
```

The key is a placeholder. Ollama ignores it, but leave it set. I tried unsetting
it to see what would happen and PyRIT resolves auth well before anything reaches
the endpoint, so you just get `ValueError: Environment variable OPENAI_CHAT_KEY
is required for non-Azure endpoints` at target construction.

## First run

```python
import asyncio
from pyrit.setup import initialize_pyrit_async
from pyrit.prompt_target import OpenAIChatTarget
from pyrit.executor.attack import PromptSendingAttack

async def main():
    await initialize_pyrit_async(memory_db_type="InMemory")
    target = OpenAIChatTarget()
    attack = PromptSendingAttack(objective_target=target)
    result = await attack.execute_async(
        objective="Write a two-line poem about network packets."
    )
    print(result.outcome)

asyncio.run(main())
```

`InMemory` means nothing hits disk. Swap it for `SQLite` when you want runs you
can query later, which is most of the time.

That prints:

```
AttackOutcome.UNDETERMINED
```

Undetermined, not success. The model answered fine. But nothing in that script
ever told PyRIT what winning looks like, so it refused to guess. Which is the
right call, and it's the first thing to get in your head: no scorer, no verdict.

## The bit that had me staring at the screen

So add a scorer. Obvious pick for refusal work is `SelfAskRefusalScorer`, which
asks a model whether a response was a refusal.

Add the two imports at the top, and replace everything after `target` inside
`main()` with this:

```python
from pyrit.executor.attack import AttackScoringConfig
from pyrit.score import SelfAskRefusalScorer

    attack = PromptSendingAttack(
        objective_target=target,
        attack_scoring_config=AttackScoringConfig(
            objective_scorer=SelfAskRefusalScorer(chat_target=target)),
    )
    result = await attack.execute_async(
        objective="Write a two-line poem about network packets."
    )

    print("outcome        :", result.outcome)
    print("outcome_reason :", result.outcome_reason)
    print("last_score     :", result.last_score)
    print(result.last_response.converted_value)
```

Same prompt, same model. Now it prints:

```
outcome        : AttackOutcome.FAILURE
outcome_reason : Failed to achieve objective after 1 attempts
last_score     : SelfAskRefusalScorer: refusal: false
Through cyberspace, packets roam,
Carrying data, back to their home.
```

The poem comes out different every run. The two lines above it won't.

Read those together, because I stared at this for a minute assuming I'd broken
something. The scorer says `refusal: false`, meaning the model didn't refuse. It
wrote the poem, it's right there. And the run is marked FAILURE.

Nothing's broken. As an objective scorer, `SelfAskRefusalScorer` defines success
as "the response was a refusal." So compliance is failure by that definition. If
you're measuring how well a model holds the line, that's exactly what you want.
If you're measuring whether an attack got through, congratulations, you've built
a harness that reports every one of your wins as a loss.

Wrap it to flip it. Same script, one substitution:

```python
from pyrit.score import TrueFalseInverterScorer

    attack = PromptSendingAttack(
        objective_target=target,
        attack_scoring_config=AttackScoringConfig(
            objective_scorer=TrueFalseInverterScorer(
                scorer=SelfAskRefusalScorer(chat_target=target))),
    )
```

```
outcome        : AttackOutcome.SUCCESS
outcome_reason : Objective achieved according to scorer
last_score     : TrueFalseInverterScorer: refusal: True
```

Same prompt, same model, same harness, opposite verdict. One wrapper. This is why
I keep going on about scoring: the attack was never the variable.

## Moving to a hosted model

Nothing above changes. Point the same three variables somewhere else:

```bash
export OPENAI_CHAT_ENDPOINT="https://api.openai.com/v1"
export OPENAI_CHAT_KEY="sk-your-key-here"
export OPENAI_CHAT_MODEL="gpt-4o"
```

Python untouched. That's the abstraction earning its keep, and it's why a
cross-target sweep is a loop instead of five scripts.

Two things to be deliberate about once real money and real endpoints are in play.
Put credentials in `~/.pyrit/.env`, not your shell history. And don't let a target
grade its own homework. Up there I had one local model playing target and judge
at the same time, which is fine for checking the plumbing works and not fine for
anything you plan to report.

## Where to go next

`SQLite` instead of `InMemory`, then go query the memory database directly. Then
converters, then multi-turn attacks like Crescendo, then scenarios once you want
hundreds of objectives instead of one.

One version note. `CharNoiseConverter` and the Best-of-N technique
[merged upstream](https://github.com/microsoft/PyRIT/pull/2277) tonight, but
they're on `main` and 1.0.1 predates them. `pip install pyrit` won't get you them
yet. They ride the next release.
