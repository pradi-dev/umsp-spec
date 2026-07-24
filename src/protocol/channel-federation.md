# Channel Federation

See [Federation Model](../architecture/federation.md) for the rationale.
This section specifies the mechanics.

## Event graph

A channel's history is a directed acyclic graph (DAG) of signed events,
each referencing the event(s) it directly follows. Every homeserver with
at least one member in the channel **MUST** maintain its own replica of
this graph, starting from the point one of its users was added as a
member.

## Delivery

When a homeserver originates a channel event, it **MUST** deliver it to
every other homeserver currently participating in the channel. Delivery
**SHOULD** be retried with backoff if a destination homeserver is
unreachable.

## No backfill

A homeserver **MUST NOT** request or accept a channel's prior history
when one of its users is newly added as a member. Its replica begins
empty and grows only from events delivered from that point forward — see
[Federation Model](../architecture/federation.md#no-backfill) for why
this trade-off is accepted.

## Membership and ownership

A channel has an **owner** account — by default, whoever created it —
who alone **MUST** be permitted to add or remove members.

```http
GET /umsp/v1/channels
Authorization: Bearer <token>
```
Returns the channels the authenticated user currently belongs to.

```http
POST /umsp/v1/channels/{channel_id}/members
Authorization: Bearer <owner's token>
Content-Type: application/json

{ "user": "bob@host-b.tld" }
```
Adds `bob@host-b.tld` as a member. From this point on, `bob`'s homeserver
**MUST** be included in [delivery](#delivery) for this channel — it
receives nothing from before this call (see [No backfill](#no-backfill)).

```http
DELETE /umsp/v1/channels/{channel_id}/members/bob@host-b.tld
Authorization: Bearer <owner's token>
```
Removes `bob@host-b.tld` as a member. Their homeserver **MUST NOT**
receive further events for this channel after removal. This endpoint does
not require or imply deleting history their homeserver already
replicated before removal.

A homeserver **MUST** reject calls to the membership endpoints from
anyone other than the channel's owner with `403`.

## Verification

Every event received via federation **MUST** be verified against its
`origin` homeserver's published signing key (see
[Identity & Key Management](./identity.md)) before being accepted into the
local replica.

> **Open issue:** conflict resolution / state-resolution algorithm for
> concurrent events (e.g. two homeservers changing channel state at the
> same time), and whether ownership can be transferred, are not yet
> specified.
