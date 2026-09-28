# NIP-1971: Problem Tracking
Kind `31971`

A problem is an addressable event. Its identity is its coordinate:

`31971:<problem creator pubkey>:<d>`

Problems form trees. The root problem deploys the scope of the tree, everything
below it is a problem that fits inside that scope. A tree may be rooted by a
`kind 31108` Rocket, in which case the problem is that Rocket's problem
statement (`MSBR334000`).

This document describes **what deployed clients actually publish**. It is a
description of the wire format in production, not an ideal design. Every row
carries the number of live events observed using that tag; where the previous
version of this document and deployed clients disagreed, deployed clients won.

> **Evidence.** Counts are from a corpus of 46 `kind 31971` events retrieved in
> September 2026 with `{"kinds": [31971], "limit": 500}` against
> `wss://nos.lol` and `wss://nostr.wine` (the only relays that returned any).
> 41 of them are NIP-1971 problems published by 4 client keys; 3 are from an
> unrelated 2023 project that reused the kind with a 64-hex `d`; 2 are from a
> second unrelated project that used `d` = `<npub>:<hex>` (both described under
> *Kind reuse*). Unless stated otherwise, the `Live` column counts all 46
> events. Client implementations MUST be tested against live events, not this
> document alone.

## Tags

| RFC 2119 | Live | Description | Spec or Example |
|---|---|---|---|
| MUST | 44/46 | Identifier. 64 lowercase hex characters, generated at random. Never derive it from the title. | `["d", "<64 hex>"]` |
| MUST | 38 | One sentence describing the problem, used as the title in clients. ≤ 140 characters. | `["title", "<up to 140 characters>"]` |
| SHOULD | 4 | Legacy title tag. Publishers MAY duplicate the title here for old clients; clients MUST accept it. | `["tldr", "<up to 140 characters>"]` |
| SHOULD | 4 | One paragraph describing the problem. ≤ 560 characters. | `["para", "<up to 560 characters>"]` |
| OPTIONAL | 3 | One page describing the problem, MAY include markdown. | `["page", "<extended description>"]` |
| OPTIONAL | 36 | A free-form description of the problem. MAY be empty. | `.content` |
| MUST | 38 | Self reference. This event's own coordinate, with the marker `origin`. | `["a", "31971:<pubkey>:<d>", "", "origin"]` |
| MUST* | 36 | Parent coordinate(s). Omitted for a tree root. MAY be repeated. | `["a", "31971:<pubkey>:<d>", "<relay hint>"]` — see *Parent links* |
| SHOULD | 37 | Root coordinate of this problem's tree. Equal to this event's own coordinate for a root. | `["A", "31971:<pubkey>:<d>", "<relay hint>"]` |
| SHOULD* | 34 | Event ID of the **genesis** (first) revision of the root problem. Omitted for a root. | `["E", "<root genesis event ID>", "<relay hint>", "<root author>"]` |
| SHOULD | 37 | Kind of the event in the root `A`/`E` tag. | `["K", "31971"]` |
| SHOULD | 34 | Kind of the event in the parent `a`/`e` tag. | `["k", "31971"]` |
| SHOULD | 37 | Author of the root problem. | `["P", "<root author pubkey>", "<relay hint>"]` |
| SHOULD | 39 | Lifecycle status, see *Lifecycle*. | `["status", "<draft \| rfm \| big \| children \| open \| claimed \| patched \| closed">]` |
| SHOULD | 7 | Status to give children of this problem when they are created. | `["child_status", "<rfm \| open>"]` |
| SHOULD | 13 | A maintainer pubkey. One tag per maintainer; clients SHOULD copy the parent's list to begin with, and MUST accept these in place of the legacy tag below. | `["p", "<maintainer pubkey>", "<relay hint>", "maintainer"]` |
| MAY | 1 | Legacy maintainer tag. Clients MUST accept it. | `["maintainer", "<maintainer pubkey>"]` |
| OPTIONAL | 34 | Pubkey of the author (or parent-scope author) being referenced. | `["p", "<pubkey>", "<relay hint>"]` |
| OPTIONAL | 18 | Event ID of the **genesis** (first) revision of *this* coordinate. | `["e", "<genesis event ID>", "<relay hint>", "genesis", "<author>"]` |
| OPTIONAL | 18 | Event ID of the revision this event replaces. Equal to the `genesis` value when this is the coordinate's second revision. | `["e", "<previous event ID>", "<relay hint>", "previous", "<author>"]` |
| OPTIONAL | 1 | Current Bitcoin block when this event was published. | `["bitcoin", "<height>:<hash>"]` |
| OPTIONAL | 1 | A Rocket this problem applies to. | `["a", "31108:<pubkey>:<rocket name>"]` |
| DEPRECATED | 34 | Event ID of the parent problem. Kept only for clients that predate coordinate-aware parent links. | `["e", "<parent event ID>", "<relay hint>", "<parent author>"]` |
| — | 0 | Claim data. Not implemented by any deployed client. | `["claim", "<pubkey>:<bitcoin height>"]` |
| — | 0 | Repository, required skill, references to other problems. Not implemented by any deployed client. | `["repo", "30617:<pubkey>:<d>"]` `["skill", "golang"]` `["a", "31971:<pubkey>:<d>", "mention"]` |

\* MUST for a non-root problem, MUST NOT for a root.

### Title resolution

A client MUST resolve the title in this order, stopping at the first
non-empty value:

1. `title`
2. `tldr`
3. `para`
4. the first line of `.content`
5. `"Untitled problem"`, with the coordinate shown so the problem stays addressable

38 of the 41 NIP-1971 events use `title`. The 3 remaining events (an October
2024 prototype) use only `tldr`, which is why `tldr` MUST still be accepted.

## Identity, revisions and replacement

`d` identifies a problem **inside its creator's namespace**. The full coordinate
is the problem's identity and it never changes, even when the event that first
defined it disappears from relays.

Events published by maintainers using the same `d` are replacements if they are
newer. A replacement MAY be published by a **different key** than the creator:
a contributor logs a problem from their own key, and a maintainer later
re-publishes it from the Rocket key. This is deliberate, and deployed clients
implement it as follows (4 live examples across 4 coordinates; 18 live events
in total carry the lineage tags below):

* the replacement keeps the creator's `pubkey` in the origin coordinate
  (`["a", "31971:<creator pubkey>:<d>", "", "origin"]`) — it does **not**
  re-point the coordinate at the maintainer;
* the replacement records its lineage with `genesis` and `previous` markers,
  each carrying the author of the referenced revision in the last slot;
* the event's own signature pubkey is the maintainer who published it.

Therefore:

> **Clients MUST group revisions by the origin coordinate**, never by
> `(event.pubkey, d)`. Grouping by `(event.pubkey, d)` renders the same problem
> once per key that ever touched it, with contradictory statuses, and inflates
> every count in the UI.

* `genesis` names the first revision of the coordinate and never changes.
* `previous` names the immediately preceding revision. If only one prior
  revision exists, both tags carry the same event ID (9 of 18 live examples).
* The current revision is the newest event for the coordinate that no other
  event names as `previous`, published by the creator or a maintainer.

### Event references rot; coordinates do not

Relays drop replaced revisions, so **event ID references to other problems
break over time while coordinate references do not**. Measured in the corpus:

| Reference style | References | Still retrievable |
|---|---|---|
| Parent coordinate (`a`) | 36 | 36/36 (11/11 distinct parents) |
| Parent event ID (`e`) | 34 | 5/34 (5/9 distinct targets) |
| Lineage (`genesis`/`previous`) | 36 | 4/26 distinct targets |
| Root event ID (`E`) | 34 | 2/2 distinct targets, of which 1 is gone |

24 of the 29 dangling parent-event references point at a single pruned event:
the genesis revision of *"Nostrocket is not currently applicable to more than a
few people"*, which has 24 children and has since been revised. A client that
follows parent event IDs loses that entire subtree; a client that follows
parent coordinates loses nothing.

Consequences:

* Clients MUST treat a missing `e`, `E`, `genesis` or `previous` target as
  normal, not as an error, and MUST NOT use an unresolvable event ID to decide
  whether a problem exists.
* Clients SHOULD resolve a parent through its `a` coordinate, and SHOULD fetch
  parents by coordinate (`#d` + `authors`), not by ID.
* A client MUST NOT conclude that a problem is a root just because its parent
  event ID cannot be fetched. Omission of the parent `a` tag is the only
  reliable root signal.

## Parent links

A parent edge is an `a` tag that points at another `31971` coordinate and is
**not** the `origin` self-tag. Two spellings exist in the wild and clients MUST
accept both:

| Live | Spelling |
|---|---|
| 34 | `["a", "31971:<pubkey>:<d>", "<relay hint>"]` — slot 2 is a relay hint (`wss://…`, or the client-local hint `cache:indexeddb`) |
| 2 | `["a", "31971:<pubkey>:<d>", "parent"]` — slot 2 is the literal marker `parent` |

Because slot 2 is a relay hint in the dominant spelling and a marker in the
legacy spelling, a client MUST ignore slot 2 values that are neither a URL nor
a recognised local hint, and MUST treat any non-`origin` `a` tag pointing at a
`31971` coordinate as a parent.

* Multiple parent tags are permitted (a DAG, not a tree). No deployed client
  has published more than one, and 36/36 live problems have exactly one parent.
* Clients MUST walk the graph with a visited set: nothing prevents cycles on
  the wire even though the protocol assumes a DAG.
* The deprecated parent `e` tag MUST NOT be used to construct the graph. It is
  informational, it is stale by design, and it MAY be omitted by publishers.

## Root scope

A tree root is a problem that omits the parent `a` tag and sets `A` to its own
coordinate (3 live roots). Every other problem in the tree carries:

* `A` — the root's coordinate;
* `E` — the root's **genesis event ID**, so that the root pointer stays
  identical even as the root problem is revised;
* `K`/`k` — `31971`, the kind of the referenced event;
* `P` — the root problem's author.

Discovery:

* To fetch one tree, subscribe to `{"kinds": [31971], "#A": ["<root coordinate>"]}`.
* To browse every published tree, page over `{"kinds": [31971]}` and group by
  the `A` tag; roots are the events where `A` equals their own origin coordinate.
* A problem with no `A` tag belongs to no known tree. Clients MUST NOT drop it:
  it is still a valid problem with a valid coordinate (4 live examples,
  including the most recently published event in the corpus).

## Maintainers

Maintainers are the pubkeys permitted to replace a problem and to move its
status. A client resolves the maintainer list in this order:

1. `["p", "<pubkey>", "<relay hint>", "maintainer"]` tags on the event — the
   dominant live spelling (13 live examples);
2. the legacy `["maintainer", "<pubkey>"]` tag (1 live example);
3. otherwise the parent problem's list, then the tagged Rocket, then the
   Rocket's repo.

The origin coordinate's creator is always permitted to replace their own
problem. Clients SHOULD copy the parent's maintainer list into a new child.

## Lifecycle

| Status | Meaning | Valid next states |
|---|---|---|
| `draft` | Published but not yet ready for participants. | `open`, `closed` |
| `rfm` | *Request For Maintainers*: the problem is in scope but nobody has committed to it. A maintainer adopts it by moving it to `open`, or rejects it with `closed`. | `open`, `closed` |
| `open` | Actionable and unclaimed. Problems solvable in roughly 6 hours of work or less can be claimed directly. | `children`, `claimed`, `patched`, `closed` |
| `children` | Too big to solve directly; has open children. | `open` (only once all children are closed) |
| `big` | Too big to solve directly and needs children to be defined. | `children` |
| `claimed` | Claimed by a participant who is expected to produce a solution within 144 blocks. | `open`, `patched`, `closed` |
| `patched` | A solution exists and is waiting for review or merge. | `open`, `claimed`, `closed` |
| `closed` | Done, or rejected. | `open` (re-opening) |

New problems begin as `open` unless the parent's `child_status` says otherwise.

* 39 of the 41 NIP-1971 events carry `status`; the observed distribution is
  `open 22, rfm 6, patched 6, big 2, children 2, closed 1`.
* `draft` and `claimed` have never been published by a deployed client. They
  are retained here because the `claim` flow below requires them.
* Clients MUST treat a missing or unrecognised `status` as `open`.
* Clients MUST display `rfm` as *Request For Maintainers*; `rfm` was previously
  listed in the enum without ever being defined.

### Claiming

`["claim", "<pubkey>:<bitcoin height>"]` is specified but **not implemented**
(0 live events). The intended flow is:

1. a participant publishes a `kind 1` reply to the problem including
   `["claim"]`, and MUST reference the problem **by coordinate**, not by event
   ID, so the reply does not rot when the problem is revised:
   `["a", "31971:<pubkey>:<d>", "<relay hint>"]`;
2. a maintainer republishes the problem with `status` `claimed` and the `claim`
   tag recording the claimant and the Bitcoin height;
3. if no solution arrives within 144 blocks, a maintainer returns the problem
   to `open`.

Maintainers may claim a problem by updating their own event's `claim` tag
without publishing a reply, though they SHOULD still reply.

## Kind reuse — clients MUST validate

`kind 31971` is not exclusively NIP-1971. Two unrelated projects are already
using it, and both will break a naive parser.

The first (2 events, 2026-06) publishes `d` = `<npub>:<64 hex>` — 128
characters, not 64 — with `e` IDs of 40 hex characters that are not NIP-01
event IDs, and the tags `["validity", "valid" | "invalid"]`, `["c", "…"]` and
`["client", "WalletScrutiny.com", "31990:…"]`. It has no `status` and no
`title`, so its status tag is `["s", …]`.

The second (3 events, 2023-10, predating the rewrite of this document) sets
`d` to an event ID and uses the `e` markers
`root`/`anchor`/`commit`/`parent`/`rocket`, `["s", "open"]` for status and
`["h", "<height>:<hash>"]` for the block.

Client requirements:

* MUST ignore an event whose `d` tag is not 64 lowercase hex characters;
* MUST ignore a `31971` event that has neither a `title`/`tldr` nor an `origin`
  `a` tag — it is not a NIP-1971 problem;
* SHOULD NOT reuse `h`, `s`, `c` or `validity` for NIP-1971 concepts, to keep
  the collision diagnosable.

## Changes from the previous version of this document

* **`title` replaces `tldr` as the title tag** (38 of 41 live problems vs 4,
  of which only 3 use `tldr` alone). `tldr` is kept as an accepted legacy alias,
  and the fallback chain is now normative.
* **The `origin` self-tag is specified** — it was live-only, and it is what
  makes the coordinate stable across authors and revisions.
* **Root scope tags `A`, `E`, `K`, `k`, `P` are specified** — previously
  undefined NIP-22-style root references. `E` deliberately names the root's
  genesis so it cannot go stale.
* **Cross-author replacement is specified.** The previous text ("replacements by
  maintainers with the same `d`") could not be implemented, because a relay
  keeps a separate coordinate per author. Clients now group by the origin
  coordinate, and lineage uses `genesis`/`previous`.
* **`genesis` is added and `previous` is restored.** `previous` was deleted in
  `e87333d` while clients kept publishing it; both markers are live vocabulary.
* **Parent links MUST be coordinates.** The parent `e` tag is deprecated after
  29 of 34 references were measured dangling.
* **Maintainers are `["p", …, "maintainer"]`** (13 live) with the dedicated
  `maintainer` tag demoted to a legacy alias (1 live).
* **`rfm` and `draft` are defined**, and all eight statuses now appear in the
  lifecycle table. `rfm` previously appeared in the enum with no meaning and no
  transitions.
* **The `claim` contradiction is fixed** — the reply is a `kind 1` that tags the
  problem's coordinate, and the feature is marked as unimplemented.
* **Missing events are normal.** Event-ID references rot as problems are
  revised; this is now an explicit client requirement rather than an oversight.
* **The kind number is consistent.** `README.md` and `Merits.md` said `1971`;
  `31971` is what is deployed.
