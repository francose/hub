---
layout: post
date: 2026-08-07 21:00:00 -0400
title: "A Search Party playbook: pivots, framework, tooling"
tags: [osint, tracelabs, ctf, methodology]
---

Four hours, one missing person, a brief that's usually three paragraphs and a
police URL. Trace Labs Search Party gives you no infrastructure to attack and no
flags to capture in the normal sense. The only thing that scores is
intelligence a human judge agrees is new and actionable.

This is the framework I run, why it's shaped the way it is, and what the
constraints do to your tool choices. I played one earlier this year and finished
7th, which is enough to have learned the failure modes and not enough to be
smug about it.

No findings here. Search Party intel goes to law enforcement through Trace Labs,
not onto a blog.

## The constraint that determines everything

Zero touch. You view, you don't engage. No contacting the subject, family, or
friends, no friending, no commenting, no password reset pages, no breach
credentials. Those aren't bounced flags, they're immediate disqualification.

That single rule eliminates most of what people reflexively call OSINT tooling.
Anything that authenticates, enumerates by probing, or leaves a notification is
out. What you're left with is read-only collection against public surfaces, and
your toolkit should be picked on that basis rather than on what's fashionable.

It also means the bottleneck is never access. It's judgment about what's worth
submitting.

## The pivot ladder

Work it in this order. Each rung produces the selectors for the next one.

**The brief.** Read it as a list of things you're not allowed to submit. Every
detail in it is already with law enforcement. Its value is selectors: full
legal name including middle names, age at disappearance, the geography, the
institution or employer, the date. Names in the brief are the seed, not the
finding.

**The primary account.** The subject's own profile, if one exists, is the single
highest-yield surface in the whole exercise. Public About panels give you
schooling, employers, hometown, relationships, and a photo set, and it's all
first-party. This is where most of my accepted submissions came from.

Worth being precise here, because the rules read as contradictory if you skim:
Facebook is listed as a banned source, and that's true of awareness Pages set up
*about* a missing person. Those are aggregators. The subject's own profile is a
primary source and a completely different animal.

**Account-linked identifiers.** From the primary account you get usernames,
profile photos, a display-name convention, sometimes a partial email or phone
from a poorly configured privacy setting. These are what you pivot on. A
username reused across platforms is the classic chain, but it only counts if you
can prove the accounts are the same person. A common handle with no
corroborating evidence gets rejected, and rightly.

**Community originals.** Research forums where people do their own digging can
produce genuine primary material: a family member posting detail that never
made the press, someone's original reconstruction of a timeline. That scores.
The advocacy blog that re-aggregates that same forum post does not.

**The timeline.** Last rung and the hardest. You're looking for evidence of the
subject's own activity after the missing date.

## Tooling, by the job it does

I'm deliberately describing jobs rather than binaries. The specific tool matters
less than knowing which of these four things you're doing, and tools churn.

**Username enumeration across platforms.** Feed a handle from the primary
account, get back candidate profiles elsewhere. Fast and high-volume, and it
generates far more noise than signal, so treat every hit as unconfirmed until
something correlates it to your subject. This is where people burn an hour and
submit garbage.

**Archive and cache retrieval.** Wayback and cached copies. Underrated, because
a profile that's locked down now was often wide open years ago, and the archived
version is a legitimate public source. Also your answer when a page changes
mid-competition and you need the state you actually saw.

**Image work.** Reverse image search to find the same photo on another platform
under a different handle, which is one of the cleaner ways to prove two accounts
are one person. EXIF where the original file is retrievable, though social
platforms strip it on upload so the yield is low.

**Search operators.** The least glamorous and probably the highest return per
minute. `site:`, `filetype:`, quoted exact strings, name variants including
middle names and misspellings. Most of the pivots that mattered came from a
well-constructed query, not from a tool.

Everything above reads only. Nothing in that list touches the subject, and
that's the point.

## Evidence discipline

One folder per case, with screenshots and evidence separated. Every screenshot
shows the URL bar and a timestamp. Capture at the moment you find it, because
pages change and a submission you can't re-evidence is a submission you can't
defend.

The submission format that got accepted: category and the specific rubric bullet
it satisfies, the URL, two or three sentences on why it matters to the
investigation, one line on the collection trail, screenshot attached.

## What the judges bounce

Worth knowing before you spend an hour producing it.

**Aggregators, every time.** Missing-persons aggregate sites, the police case
page, community advocacy blogs that re-aggregate. The judge note is always some
version of: this is a missing persons website, any leads such as these would
have been shared with law enforcement, and will not be considered a new lead.
You can use them to pivot to original sources. That's the entire relationship
you should have with them.

**Anything you sourced twice.** One scoring submission per unique URL.
Splitting one source across multiple categories reads as gaming the system,
which sits closer to a DQ risk than a rejection. Your URLs are the scarce
resource, not your time. Decide what a source is worth and pitch it once, in
the highest category it honestly supports.

**Category mismatches.** Read the definitions literally. Advancing the Timeline
wants activity from the subject's *own* accounts after the missing date, not a
third-party sighting reported later. I submitted post-disappearance sightings
and read the rejection as a judgment call when it was a category mismatch.
they were never eligible. Same literalism on advanced subject info, which wants
identifiers not already in press or police reports. If the police page mentions
it, it isn't advanced.

**Refinements.** "Hooded jacket" where the brief said "jacket" is not
intelligence, it's editing. What scores is anything that helps someone
recognize the person now. A judge reading a hundred submissions decides in
seconds whether yours changes anything, so the framing carries as much weight
as the finding.

Check the current point values against the published category list before the
window opens rather than trusting your notes. Mine were wrong, and Trace Labs
flagged rule changes in early 2026 that hadn't propagated into the training
material yet.

## Pick the case on the evidence

The compelling cold case is a trap, and I walked into it. A disappearance from
2010 has no live accounts to work, which means the high-value categories
structurally do not exist for you no matter how good your tradecraft is. You are
capped at a few hundred points of basic subject information.

Someone missing one to three years, with a real pre-disappearance online
footprint, is where the timeline and location flags actually live. Pick on
surface area, not on narrative.
