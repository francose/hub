---
layout: post
date: 2026-09-12 20:30:00 -0400
title: "Notes from a Maltego OSINT CTF"
tags: [osint, ctf, methodology]
---

Played the Maltego OSINT CTF this week with Team Darkstar and finished 44th with
3,881 points. The categories were all over the map: dark web, maritime,
aviation, geolocation, due diligence, and threat intelligence. Across them, a
surprising amount of time went into the gap between a finding I could support and
a flag the checker would accept. Reading a POCSAG message, decoding an ADS-B
frame, pulling a vessel's inspection record, walking a domain's DNS history:
knowing where to look got me moving. Deciding whether the evidence actually
answered the question took longer.

One vessel's inspection record listed Kocaeli on the relevant date, but Kocaeli,
Izmit, and Körfez all came back rejected. Two subdomains of the same firm shared the same GA4 and
GTM identifiers, but that pair wasn't accepted either. Those were useful
findings, and still unfinished challenges. The frustrating part was working out
whether I had a naming problem, a different interpretation of the question, or a
missing piece of evidence. Under a clock, it's easy to spend too long trying
variations of an answer you've already become attached to.

The lessons that stuck were old ones, re-learned under pressure. Know what your
identifier actually identifies, and how long that association holds. A ship's IMO
number follows it through changes of name, flag, and ownership. Aircraft
registrations and ICAO hex addresses need more care: they can change, so check
the registration history and cross-reference the manufacturer and serial number
when tracing an airframe over time. A GSTIN can turn a business-name search into
a specific registration record. Names are useful leads; the identifiers and dates
are what let you connect the records reliably.
([IMO guidance](https://www.imo.org/en/ourwork/msas/pages/imo-identification-number-scheme.aspx),
[FAA guidance](https://www.faa.gov/licenses_certificates/aircraft_certification/aircraft_registry))

Another lesson was to follow a claim back to its source. One challenge asked for
the number of days between a company's website going live and its official
registration. The company-data sites I checked gave a registration date one day
away from the national registry's own record. That one day changed the answer.

I also got a reminder about access and time. A couple of leads appeared to depend
on a breach index or authenticated LinkedIn access I didn't have. I couldn't
establish an accessible route to the answer within the time available.
Recognizing that uncertainty early, recording the lead, and moving on would have
been worth more than another hour of grinding.
