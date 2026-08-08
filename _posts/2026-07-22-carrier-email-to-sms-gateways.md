---
layout: post
title: "Carrier email-to-SMS gateways are still an open door"
date: 2026-07-22 19:00:00 -0400
tags: [opsec, messaging, phishing]
---

Every major US carrier runs an email-to-SMS gateway. Send mail to a
number@carrier-domain address and it arrives on the handset as a text. The
feature is decades old, predates anyone thinking hard about sender identity,
and is still on.

I wrote up the mechanics with runnable PoCs in
[sms_gateway_poc](https://github.com/francose/sms_gateway_poc): basic SMTP
send, direct-to-MX delivery that skips a relay entirely, multi-carrier fanout,
and `From:` header spoofing.

The interesting property is what the recipient sees. A message that arrived
over SMTP renders in the same thread UI as one that arrived over SMS, with
whatever sender string survived the gateway. The trust cues a person uses to
judge a text message were designed around the carrier network, and this path
routes around it while still landing in the same inbox.

There's a defender-side scanner in the repo, which is the part I care about
more. If you own a domain or a mail path, knowing whether your infrastructure
can be used this way is a thing you can check rather than assume.

What I'm not claiming: this isn't novel and it isn't a vulnerability in the
sense of something a carrier will patch. It's a legacy interoperability feature
behaving as designed, and that's precisely why it's durable. I also haven't
measured what fraction of messages survive carrier-side filtering, which varies
by carrier and changes without notice. Treat the PoCs as demonstrating the path
exists, not as a delivery rate.

Authorized testing only. Sending unsolicited messages through these gateways is
illegal in most places regardless of how the plumbing works.
