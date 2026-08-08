---
layout: post
title: "What Search Party judges actually reject"
tags: [osint, tracelabs, ctf, methodology]
---

I played a Trace Labs Search Party CTF earlier this year, finished 7th, and got
enough submissions bounced to learn something. Writing down the rejection
patterns because the scoring rules are published but the calibration isn't, and
I'd have saved an hour if someone had told me this up front.

No findings here. Search Party intel goes to law enforcement through Trace Labs,
not onto a blog. This is only about how the scoring works.

## Aggregators are worthless and it's not close

Every submission I sourced from a missing-persons aggregate site came back with
the same note: this is a missing persons website, any leads such as these would
have been shared with law enforcement, and will not be considered a new lead.
You can use them to pivot to original sources.

That's the whole game in one sentence. The aggregate sites, the police case
page, the community advocacy blogs that re-aggregate — all of it is already in
the file. It's a starting point for pivots and nothing else. Don't submit it.

There's a wrinkle here that reads as a contradiction if you skim the rules.
Facebook is listed as banned, and that's true of awareness Pages set up *about*
a missing person, which are aggregators like any other. The subject's own
profile is a primary source and is a completely different thing. Same split on
research forums: an original community research post can score, a blog
re-aggregating that post can't.

## One submission per URL

Multiple submissions off the same thread got killed as duplicates no matter
which category I filed them under. The rules call splitting one source across
categories "gaming the system," which puts it closer to a DQ risk than a
rejected flag.

Practical consequence: your URLs are the scarce resource, not your time. Decide
what a source is worth before you pitch it, and pitch it once, in the highest
category it honestly supports.

## Read the category definitions literally

The one that cost me the most: Advancing the Timeline wants activity from the
missing person's *own* accounts after the missing date. Not a third-party
sighting reported after the fact. I submitted sightings from a few weeks post-
disappearance and got told the dates last seen should be more recent, which I
first read as a judgment call and later understood as a category mismatch. They
weren't eligible at all.

Same literalism on Advanced Subject Info — it wants identifiers not already in
press or police reports. If the police page mentions it, it isn't advanced.

Checking the current point values against the published category list is worth
five minutes before the window opens. The numbers I had in my notes were wrong,
and Trace Labs flagged rule changes in early 2026 that hadn't propagated into
the training videos yet.

## Forward-looking beats detailed

Refinements to the existing description died. "Hooded jacket" where the brief
said "jacket" is not intelligence, it's editing.

What scored was anything that helped someone recognize the person *now* —
concrete, current, actionable for identification. The framing matters as much
as the finding, because a judge reading a hundred submissions is deciding in
seconds whether yours changes anything.

## Pick your case on the evidence, not the story

The compelling cold case is a trap. A disappearance from 2010 has no live
accounts to work, which means the high-value categories structurally don't
exist for you, and you're capped at a few hundred points of basic subject info
no matter how good you are.

Someone missing one to three years with a real pre-disappearance online
footprint is where the timeline and location flags actually live.

## The format that got accepted

Category and the specific rubric bullet. The URL, unique to that submission.
Two or three sentences on why it matters to the investigation. A line on the
collection trail that got you there. Screenshot with the URL bar and timestamp
visible.

Zero touch throughout — view, don't engage. No contacting the subject, family,
or friends, no friending, no commenting, no password reset pages. Those are
immediate disqualification, not a bounced flag.
