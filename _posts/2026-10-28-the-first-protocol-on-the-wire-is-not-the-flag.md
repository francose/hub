---
layout: post
date: 2026-10-28 15:30:00 -0400
title: "The first protocol on the wire is not the flag"
description: "Notes from Dragos CTF 2026: decoys, encodings, and the gap between what you decoded and what the checker wanted."
tags: [opsec, ctf, ics]
---

Played Dragos CTF 2026 and finished 33rd with 28,600 points. The visible
board was 57 challenges; they were all solved before I stopped. The categories
were ICS-flavored: BACnet, Modbus, S7, IEC 60870-5-104, a Game Boy ROM, a 16-bit
DOS setpoint tool, and a TN3270 session that an IT contractor had closed as
garbled TLS.

The time sink was not finding the traffic. It was deciding which object on
the wire was the answer, and in which encoding the checker wanted it.

Decoys were the first tax. A TeamViewer string in an SMB capture looked like
a flag and was not. A syslog CVE in the plant diagram was the wrong device;
the HMI CVE was the one that scored. An empty NTLM hash was a real answer
once I stopped looking for a password. A BACnet DCC password scored as the
decoded string, not the base64 sitting on the wire. The first payload in the
S7 pcap was TPKT/COTP on 102; the accepted protocol for that question was
Modbus, because 502 had been scanned. Burning the S7 names cost tries I did
not get back.

Format was the second tax. Several challenges locked after two or three
wrong submits. An S7 REAL write to `CL2_DOSE_A` is `00 04 00 20` then four
bytes: I read `41113333` as 9.07 and sat in lockout. The bytes were
`41133333`, which is 9.20. A BACnet covert channel wrote numbered base64
chunks into object descriptions; reassembled they were a PNG of
`flag{w4t3r_und3r_th3_br1dg3}`. The challenge text said this one uses `()`
and not `{}`. The checker wanted `flag(...)`. I spent the remaining tries on
the wrapper instead of the delimiter.

The encodings themselves were ordinary once named. Modbus FC16 values were
shuffled by transaction ID; sort, then the same UTF-16LE-then-base64 trick
as the first exfil. Coils were LSB-first bits packed into bytes. The
"garbled TLS" on port 3270 was EBCDIC `RETR n` plus four C fragments, XOR
`0x42`. Extra bytes after the S7 REAL decoded under XOR `0xAB`. The IEC-104
"plant" was a local Go binary whose `generateFlag` hashed a constant with
SHA-256 and never needed the process running.

None of that is a claim about production ICS, and it is not a walkthrough.
It is a reminder I already had from the Maltego CTF and re-learned on a
different board: a finding you can support is unfinished until it matches
the question the checker is actually asking.
