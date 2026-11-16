---
layout: post
date: 2026-11-16 14:35:00 -0500
title: "Your agent should not hold a credential"
description: "Design for an agent authority ledger on Hyperledger Fabric 3: every tool call authorized by chaincode before it runs, a proxy you don't have to trust, and the ten attacks I'll run against it."
tags: [ai-security, agents, hyperledger, zero-trust]
---

*Design post. No results yet; the attack matrix at the end runs next.*

An agent with an API key is a confused deputy waiting for the right prompt.
This post is the design I'm building in a lab to replace that key: every
tool call the agent makes is authorized by chaincode on a Hyperledger Fabric
3 channel before it runs, the result is written back before the agent sees
it, and the proxy that brokers all of this is deliberately something you
don't have to trust.

Nothing here has been attacked yet. The attack matrix is at the end. The
results post comes after I run it.

What I'm claiming: the unit of authority for an agent should be one call,
decided by a quorum, with the decision recorded where no single operator can
rewrite it.

What I'm not claiming: that you need a blockchain. Fabric is the cheapest way
I know to get multi-org endorsement, BFT ordering, and append-only history in
one box. A signed transparency log plus a policy engine gets you most of the
way, and the design below works there too.

## What a prompt injection buys today

The agent holds a long-lived key, scoped to a role, because that's how keys
work. An injection lands through a tool result. From that point the agent
spends the full scope of the key: every endpoint the role allows, for as long
as the key lives. The only record of what happened is the tool's own log,
written by the component that just got compromised. Revocation means rotating
the key and redeploying.

Four things are wrong at once: the grant is per session, the check happens at
the callee, the audit is mutable by the callee, and the blast radius is the
whole role. Change the unit from credential to authority and all four move.

| | Credential (today) | Authority (this design) |
| --- | --- | --- |
| What is granted | A key with a role, valid until rotated | Permission for one call, valid once |
| Where it is checked | At the API edge, by the callee | Before execution, by a quorum of orgs |
| Blast radius of an injection | Full scope of the key | The calls policy allows for that task |
| Audit record | Callee's log, mutable by the callee | Endorsed ledger, append-only |
| Revocation | Rotate the key, redeploy | One ledger transaction |
| Attribution | Shared service account | Per-agent X.509 on every transaction |

## Why a ledger at all

The question I get most when I show this: why drag a blockchain into it? A
database with a policy engine in front of it decides allow or deny just fine.

It does, for one trust domain. The use case I have in mind has several
parties who need to agree on what an agent may do, and who need to believe
the record of what it did, and none of them should have to trust the others'
servers.

Inside one company that's already three teams. Security writes the policy,
platform runs the agents, the application team owns the tools and the data.
Today the audit log lives with whichever of those teams runs the logging
pipeline, and that team can edit it. Across companies it's sharper: a
vendor's agent operating against a customer's systems, or two companies'
agents transacting with each other. Each side wants the other's actions
authorized under rules it co-signed and recorded somewhere the other side
can't quietly rewrite. That's also where LF Decentralized Trust's new AI
working group is pointing: agent identity and agent-to-agent transactions
across organizational boundaries.

Five properties fall out of that requirement, and Fabric happens to package
all five:

- Multi-party endorsement. A decision is valid only if peers from each org
  ran the same chaincode on the same inputs and signed the same result. No
  single team's server gets to say allow.
- Policy as replicated code. The policy is the chaincode. Every org runs the
  same version, and changing it is itself a transaction every org must
  endorse.
- Signed, ordered, append-only history with a full copy at every org. An
  insider on the platform team can't delete the record of what the agent
  did, because the same record lives at security and at the app team.
- Revocation that every party sees in the same block.
- Byzantine tolerance. The ordering layer keeps going with one malicious
  node, which is what "the infrastructure is partly hostile" looks like in
  practice.

What that buys in concrete terms: an incident responder asks whether the
agent touched customer data and gets an answer cross-signed by three teams
rather than a log from the team under review. An auditor gets a read-only
peer and verifies the chain themselves instead of trusting an export from
the operator's SIEM. A dispute between a vendor and a customer over what an
agent did resolves to a grant and a record both sides endorsed.

If you have one team and one trust domain, do this with OPA and a signed
transparency log. You get append-only and verifiable at a fraction of the
operational cost, and you lose only multi-party endorsement, which you
didn't need. The ledger starts paying for itself at the second party.

## The design

![Architecture: every tool call passes the proxy, which asks the ledger first]({{ '/assets/img/agent-authority-ledger/architecture.svg' | relative_url }})

Four parts.

The agent is an LLM with an MCP client and nothing else. No keys, no
credentials, one network it can reach.

The MCP proxy is a Go service with a Fabric Gateway client. It canonicalizes
the tool arguments (RFC 8785 JCS), hashes them, and calls
`Authorize(agent, tool, argsHash)` on the channel. The call returns a
decision and a nonce once the grant has committed. On allow, the proxy
executes the tool with credentials only it holds, then calls
`Record(nonce, resultHash)`. On deny, nothing runs and the agent gets a
refusal.

The Fabric channel runs the `agentauth` chaincode on peers from two
organizations, with an endorsement policy that requires both. Four orderers
run SmartBFT, so one can be malicious and the channel keeps going. Fabric CA
issues a short-lived X.509 per agent instance. The proxy sidecar holds it;
the LLM process never sees it.

The ledger holds five key spaces: `agent:[id]`, `policy:[id]`,
`grant:[nonce]`, `action:[nonce]`, and `revocation:[id]`. Raw arguments go
to a private data collection keyed by the same nonce; only the hash is on
the channel. A grant is written once and consumed once.

Why Fabric 3 and not the 2.5 LTS: 3.0 added the BFT ordering service and 3.1
added Ed25519. The Byzantine-orderer test in the matrix needs the first.

## The proxy is not the control

The first thing anyone asks: what if the agent goes around the proxy? With
the proxy as the only enforcement point, four things get you around it, and
an IP allowlist on the tool network stops none of them.

1. Confused deputy. The agent doesn't need to reach the tools. It needs to
   make the proxy do it. A `tool-http` call with a URL pointing at `tool-db`
   passes every IP check, because the request really did come from the
   proxy.
2. Source spoofing. Docker's default capability set includes `NET_RAW`. A
   container on the same bridge can forge the proxy's source address or
   ARP-spoof it.
3. Proxy compromise. Tool output flows back through the proxy's parser. One
   bug there and the attacker holds every credential and every network.
4. Transport. Agent and proxy in one container over stdio means there's no
   network boundary at all.

The fix is structural: tools stop trusting the proxy and trust the ledger. A
credential broker (Vault or SPIRE) mints a short-lived credential only
against a grant that exists on the channel, and the credential carries the
nonce. The tool checks the credential and the args hash itself. A direct
call, a spoofed address, or a compromised proxy without a grant now all fail
the same way, and the proxy becomes one ledger client among others.

An agent that refuses the proxy outright gets nowhere. It has no Fabric
identity, so it can't call `Authorize`. No grant, so the broker won't mint.
No route and no credential, so the tool refuses. Every attempt is a
`connect()` the kernel probe records. Give the agent its own identity later
and this still holds: chaincode evaluates policy by identity, and the tool
verifies the hash regardless of who submitted.

## Consensus has a cadence

The requirement I set myself was that every action is a transaction, goes
through consensus, and can't be reversed. The honest version of that
requirement is that consensus has a cadence, and you have to pick which
actions get it. There are five places to stand between an agent and the
world, and only some of them can wait for a block.

| Layer | Interception point | Fits consensus per event? |
| --- | --- | --- |
| Model I/O | Inference gateway in front of the LLM | Yes, tens per minute |
| Tool call | MCP proxy, or the framework's tool-dispatch hook | Yes, the natural grain |
| Credential | Vault or SPIRE issuing against a ledger grant | Yes, and tools need no changes |
| Network | Envoy ext_authz or a CNI policy on all egress | Borderline, hundreds per second under load |
| Kernel | seccomp-notify, eBPF LSM, Tetragon | No, thousands per second |

I picked two gates and one anchor.

![Three capture layers: two gate the action, one anchors the evidence]({{ '/assets/img/agent-authority-ledger/capture-layers.svg' | relative_url }})

Intent, at the tool call, and authority, at credential issuance, both commit
before anything happens. A deny costs nothing. An allow is attributable
before execution. Tens of events a minute, one block each.

Effect, at the kernel, can't wait. An eBPF probe on the agent and tool
cgroups sees `execve`, `connect`, and `openat` at thousands a second. Those
get hash-chained on the host and a Merkle root goes to the channel once a
second. That is tamper evidence and only that. Enforcement at this layer
comes from a seccomp or Cilium policy whose hash was itself a grant.

I skipped the network layer for v1. The credential layer covers most of what
it would catch, with one exception: the LLM endpoint is egress. So the
inference gateway has to be the agent's only route out, and it has to
enforce too.

## Irreversible has to be designed in

Fabric's world state is mutable. Chaincode can overwrite a key; only the
block history is append-only. So three rules in the chaincode and the
channel config:

- Grant and action keys are write-once. Any `PutState` on an existing key is
  rejected.
- The chaincode lifecycle endorsement policy requires every org. No single
  org can ship a chaincode version that rewrites grants.
- Action records stay out of private data collections. Fabric 2.5 and later
  can purge those, which is a feature for GDPR and a bug for an audit log.

And be exact about the guarantee. Irreversible here means it takes collusion
of a quorum of orgs plus more than f orderers. In a single-enterprise
deployment the orgs are security, platform, and the owning application team.
Whether those three colluding is in your threat model is a question for you,
and the design doesn't answer it.

## The test plan

Ten attacks, each crossing one trust boundary. B1 is agent to proxy, B2 is
proxy to tool, B3 is proxy to ledger, B4 is inside the ledger. The expected
control is the claim under test. None of these have run.

| # | Attack | Boundary | Expected control |
| --- | --- | --- | --- |
| 1 | Prompt injection calls an unregistered tool | B1 | Policy denies; a deny grant lands on the ledger |
| 2 | Argument tampering after authorize (TOCTOU) | B2 | Proxy executes only the authorized hash; the tool re-checks it |
| 3 | Nonce replay | B1 | Single-use nonce, consumed by Record |
| 4 | Revocation latency | B3 | Revoke commits in one block; the next Authorize denies |
| 5 | Compromised peer endorses everything | B4 | Endorsement needs both orgs; ENDORSEMENT_POLICY_FAILURE at validation |
| 6 | Stolen agent certificate | B3 | Short-lived cert, fingerprint bound to the agent, channel CRL |
| 7 | Byzantine orderer, f = 1 of 4 | B4 | SmartBFT tolerates f < n/3; liveness holds, no fork |
| 8 | Proxy bypass, direct to tool | B2 | Tool demands a nonce-bound credential |
| 9 | Authorize flooding | B1 | Per-agent rate limit in the proxy, per-agent quota in chaincode |
| 10 | Agent calls Authorize with its own identity | B1 | Allowed only where policy allows; the tool still checks the hash |

Row 10 is the one I care about. If it passes, the proxy isn't load-bearing,
and that was the point of the design.

## What ships next

In order, each runnable before the next starts: the Fabric 3.1 test-network
with four BFT orderers; the `agentauth` chaincode in Go; the MCP proxy; any
MCP-speaking agent pointed at it; one harness script per row above.

Three things are open before I write the chaincode. Whether the proxy waits
on endorsement or on commit, since one block interval per tool call is the
latency bill. Whether the policy language stays a per-tool allow-list with
argument regexes or moves to Cedar evaluated inside chaincode, with the
determinism risk that brings. And whether the broker enrolls one identity
per agent instance or per task.

Adjacent work I'll compare against: Caracal, an LF Decentralized Trust lab
that lists the same properties (policy-based authorization, least-privilege
delegation, instant revocation, tamper-evident audit). I haven't read its
source yet, so I'm not claiming a difference until I have. Proof-of-Control,
another LFDT lab, is a verification standard and a candidate format for the
`Record` entries.

If you see where this bends, tell me before I build it. Results, with the
repo, in the next post.

## Sources

- [What's new in Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/latest/whatsnew.html):
  v3.0 BFT ordering, v3.1 Ed25519, v2.5 as LTS
- [Hyperledger Fabric 3.0 release announcement](https://www.lfdecentralizedtrust.org/announcements/version-3.0-of-hyperledger-fabric-an-lf-decentralized-trust-project-now-available)
- [Hyperledger Fabric v3: SmartBFT consensus](https://lfdecentralizedtrust.org/blog/hyperledger-fabric-v3-delivering-smart-byzantine-fault-tolerant-consensus)
- [Caracal](https://www.producthunt.com/p/github-445/caracal-2): LFDT lab,
  authority layer for AI agents
- [LFDT meetups: Proof-of-Control launch, LFDT AI Working Group](https://www.meetup.com/hyperledger-lisbon/)
- [RFC 8785, JSON Canonicalization Scheme](https://www.rfc-editor.org/rfc/rfc8785)
