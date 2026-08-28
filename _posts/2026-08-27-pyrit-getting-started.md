---
layout: post
title: "PyRIT from zero: install, first run, and the wire that flips your verdict"
date: 2026-08-27 20:45:00 -0400
tags: [ai-security, pyrit]
---

The walkthrough below was run end to end on a clean virtualenv tonight, against a
model running locally, with no API key involved. The hosted-endpoint swap at the
bottom is the documented path rather than something I re-ran.

## What PyRIT is

PyRIT is Microsoft's open source framework for testing generative AI systems. The
short version: it is the plumbing between a prompt and a verdict. You give it a
target, an objective, and a way to score the answer, and it runs the loop and
writes everything to a database you can query afterward.

The pieces are worth naming up front, because everything else is composed out of
them. A **target** is
whatever you are testing, an OpenAI endpoint, a local model, an HTTP API, a web
app driven through Playwright. A **converter** transforms a prompt before it is
sent, base64, ROT13, character noise. A **scorer** decides whether a response
counts as success. An **attack** wires those together and drives the
conversation. A **scenario** packages many attacks and datasets to run at scale.

Everything else in PyRIT is a variation on those five.

## Why it is worth using

The strongest thing PyRIT gives you is comparability. Attacks are the easy part,
most published ones are a few dozen lines. Producing a result that someone else
can reproduce is the hard part, and that is the problem this framework actually
solves.

It solves it well. Every prompt, response and score lands in a memory database
you can query afterward, so a run is an artifact rather than a scrollback buffer.
Scorers are named, documented components instead of a regex for "I can't help
with that". Targets sit behind one abstraction, which means OpenAI, Azure,
Anthropic, Google, HuggingFace, raw HTTP endpoints, WebSockets and
Playwright-driven web apps are all the same shape to your code, and swapping
gpt-4o for a model on your laptop is one environment variable rather than a
rewrite. The scorers compose, which matters more than it sounds like it should,
and I will come back to that.

It is also a project that takes contributions seriously. When I submitted
Best-of-N, a maintainer reshaped it onto PyRIT's technique abstraction and merged
it, which produced a better result than the patch I sent. Papers get cited
properly in `references.bib`. That kind of care is why the work accumulates here
instead of scattering across a hundred one-off repos, and it is a large part of
why I would rather put a technique into this than publish my own tool.

## Who should be using it

Anyone who has to answer "is this model safe enough to ship" with evidence.
Red teamers who are already doing this by hand and want the results to survive
contact with a second person. And anyone about to build an eval harness from
scratch, if only to see what you would be reimplementing before you commit to
it.

If you just want to poke at a chatbot for an afternoon, you do not need a
framework for that.

## Install

Python 3.10 through 3.14. Check first, because the range has a ceiling and 3.15
is not in it.

```bash
python --version
python -m venv .venv && source .venv/bin/activate
pip install pyrit
```

That gets you the latest release from PyPI, which is 1.0.1 as of tonight. Confirm
it, because the website documents the `main` branch, which runs ahead of the
latest release:

```bash
python -c "import pyrit; print(pyrit.__version__)"
```

If that prints something other than what the docs assume, read the docs on the
matching release branch instead:
`https://github.com/microsoft/PyRIT/tree/releases/v1.0.1/doc`.

## Point it at a local model

The quickest target that costs nothing is Ollama, which speaks an
OpenAI-compatible API. PyRIT documents this directly. Pull a model and set three
variables:

```bash
ollama pull llama3.2:3b

export OPENAI_CHAT_ENDPOINT="http://127.0.0.1:11434/v1"
export OPENAI_CHAT_KEY="not-needed"
export OPENAI_CHAT_MODEL="llama3.2:3b"
```

The key is a placeholder. Ollama ignores it, but leave it set: PyRIT resolves
auth before it ever reaches the endpoint, and unsetting it fails at target
construction with `ValueError: Environment variable OPENAI_CHAT_KEY is required
for non-Azure endpoints`.

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

`InMemory` means nothing is written to disk. Swap it for `SQLite` when you want
runs you can query later, which is most of the time.

That prints:

```
AttackOutcome.UNDETERMINED
```

Undetermined, not success. The model answered fine. But nothing in that script
told PyRIT what winning looks like, so it declined to guess. That is the correct
behavior and it is the first thing worth internalizing: no scorer, no verdict.

## The part that decides your answer

So add a scorer. The obvious pick for refusal work is `SelfAskRefusalScorer`,
which asks a model whether a response was a refusal.

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

The poem will come out different every run. The two lines above it will not.

Read those two lines together. The scorer says `refusal: false`, meaning the
model did not refuse. It wrote the poem. And the run is marked FAILURE.

Nothing is broken. As an objective scorer, `SelfAskRefusalScorer` defines success
as "the response was a refusal." Compliance is failure by that definition. If you
are measuring how well a model holds the line, that is exactly right. If you are
measuring whether an attack got through, you want the opposite polarity, and you
have to say so.

PyRIT gives you that as a wrapper rather than a second scorer to write. Same
script, one substitution:

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

Same prompt, same model, same harness, opposite verdict. One wrapper. This is the
whole reason I keep writing about scoring: the attack was never the variable.

That is the composability paying off. Scorers are primitives you assemble, so
between `TrueFalseInverterScorer` and `TrueFalseCompositeScorer` you can build
the exact question you mean rather than going looking for a scorer that happens
to ask it. Most harnesses make you fork something to get this. Here it is a
constructor argument, and the resulting wiring is visible in your code, which
means a reviewer can see what you counted as success without reading your mind.

## Moving to a hosted model

Nothing above changes. Point the same three variables somewhere else:

```bash
export OPENAI_CHAT_ENDPOINT="https://api.openai.com/v1"
export OPENAI_CHAT_KEY="sk-your-key-here"
export OPENAI_CHAT_MODEL="gpt-4o"
```

The Python is untouched. That is the abstraction earning its keep, and it is why
a cross-target sweep is a loop rather than five scripts.

Two things to be deliberate about once real money and real endpoints are
involved. Put credentials in `~/.pyrit/.env` rather than your shell history.
And do not let a target grade its own output. Above, one local model played both
the target and the judge, which is fine for checking that your plumbing works and
not fine for anything you intend to report.

## Where to go next

`SQLite` instead of `InMemory`, then query the memory database directly. Then
converters, then multi-turn attacks like Crescendo, then scenarios once you want
hundreds of objectives instead of one.

One note on versions. `CharNoiseConverter` and the Best-of-N technique
[merged upstream](https://github.com/microsoft/PyRIT/pull/2277) tonight, but they
are on `main` and 1.0.1 predates them. `pip install pyrit` does not get them yet.
They ride the next release.

PyRIT is worth the hour it takes to get comfortable with. It is open source,
actively maintained, and the documentation is genuinely good once you know which
branch you are reading. The fastest way in is the one above: point it at a model
on your own machine and look at what it records.
