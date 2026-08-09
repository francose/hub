---
layout: post
date: 2026-08-08 19:15:00 -0400
title: "What Search Party actually pays for"
tags: [osint, tracelabs, ctf, methodology]
---

I posted a Search Party playbook the day before DEF CON 34 and then went and
played one. Several things in it were wrong. This is the correction, written
the same evening, while the rejections are still fresh enough to be honest
about.

Same rule as last time: no findings here. Search Party intel goes to law
enforcement through Trace Labs, not onto a blog. Everything below is about
method.

For grounding: 25th of 110 teams. The team put in 41 submissions and had 34
accepted. Nine of those were mine, six accepted and three rejected.
Last event I finished 7th on a cold case, so this was a step backwards on a
harder case type, and the reasons are not mysterious. Most of them are in this
post. We also got pulled out of the random prize draw, which is not a skill.

## The thing I got most wrong

I spent the first half of the window building analysis. Geocoded every
candidate location, computed distances and bearings, reconstructed which
transit route the subject could plausibly have taken by pulling public transit
relations and measuring every stop against her accommodation. It was careful
work. I ran a falsification test against my own hypothesis and reported the
result when it came back against me.

All of it scored zero. Both analytical submissions came back No Point Value.

One of them was later corroborated by an independent source that said, in
effect, exactly what my geometry had concluded. It still scored zero, because
being correct was never the test.

Trace Labs pays for **discovered artifacts, not inference**. A page that names
the subject is intelligence. An argument about the subject, however well
constructed, is not. If your submission's evidence section describes a
calculation rather than a source, you are about to spend an hour for nothing.

That distinction is obvious in retrospect and I did not see it until the second
NPV came back.

## The rejection rule I documented and then walked into anyway

Anything sourced from coverage of the disappearance gets rejected. The family
and the police feed details to the media. So by the time it is in print, law
enforcement has had it for weeks. The NPV category names newspapers explicitly.

I knew this. I had written it down. Then I recommended two submissions built on
press reporting, including one where the detail appeared in exactly one outlet
and nowhere else, and another where I could demonstrate from the raw case record
that the detail was absent from the official report. Both rejected.

Verifiable absence from the missing-person report is not sufficient. Presence in
press is disqualifying on its own.

## Where the points actually are

Every approval came from the subject's life **before** the disappearance.

Her name in a search engine returns nothing but the case. Seven weeks of
international coverage buries everything else she ever did. But she existed
before any of it, and that earlier footprint is unindexed relative to the noise,
which means nobody else on the scoreboard is standing there.

So: old local papers, school newsletters, club and project rosters, exhibition
listings, sports results, yearbooks, community organisation pages. Material that
predates the case by years and has no relationship to it.

## The pivot that broke it open

Searching the subject's name is useless for the reason above. The move is to
search the **rare names adjacent to her**.

A press photograph caption named her alongside several other people. Those other
names were uncommon strings with no connection to any missing-person story, so
they cut straight through the coverage that swamped hers. Three of the six
approvals trace back to that one caption.

Two smaller tricks from the same chain. Newspaper photo assets often carry a
desk reference in the filename, and those reference formats are house
conventions, so the ID alone can tell you which publisher and therefore which
regional title to search. And image URLs on large news sites sometimes encode
the original source URL in the asset filename, which will hand you a social post
identifier for free.

## Read the live category page, every time

My point values were wrong on five of nine categories, and I was missing a
category entirely. Not slightly wrong. Wrong in ways that would have changed
which category I filed things under and therefore what they paid.

Do not trust your notes, your last event, or a community guide. Open the
official categories page at the start of the window and read it. It takes two
minutes and mine cost more than that.

## Some rules are coach-dependent

I had one submission per unique URL written down as an absolute after a previous
event's rejections. This time the coach explicitly allowed a second flag in a
different category against the same URL.

I had spent part of the window steering away from points that were available.
Ask the coach at the start rather than importing a ruling from a different
event and a different judge.

## Where automation earned its keep, which was nowhere

Username enumeration sweeps, an API enumeration against a route-logging
platform, a vendor API, authenticated-endpoint probing. None of it produced a
scoring submission.

Two specific traps worth naming. Several large platforms return HTTP 200 with a
soft-404 body for usernames that do not exist, so a status-code sweep reports
every candidate as a hit. Always calibrate against a control string that cannot
possibly exist before you believe any enumeration result. And one major platform
returns an error for every unauthenticated profile lookup including controls,
which means no unauthenticated method distinguishes a real profile from a fake
one. Knowing that is worth more than another tool.

I also made the opposite error. My sweep did surface the subject's real handle
and I discarded it, because I had just been burned by the false positives and
bucketed it with genuinely common strings without opening it. It was her full
name on an uncommon surname and was never in that class. That single call cost
more points than every automated technique returned all night.

The lesson is not that enumeration is useless. It is that enumeration produces
candidates, and the judgment about which candidate to open is the entire job.

## The shape of a good four hours

Read the live category page. Ask the coach about URL reuse. Spend the first
thirty minutes finding out who the subject was before the case existed, using
the rare names around her rather than her own. Collect pages, not arguments.
Screenshot with the URL bar visible at the moment of discovery.

Do not build a model of what happened. Nobody is paying for your model.
