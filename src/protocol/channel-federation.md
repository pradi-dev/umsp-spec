# Channel Federation

See [Federation Model](../architecture/federation.md) for the rationale.
This section specifies the mechanics.

## Event graph

A channel's history is a directed acyclic graph (DAG) of signed events,
each referencing the event(s) it directly follows. Every homeserver with
at least one member in the channel **MUST** maintain its own full replica
of this graph.

## Delivery

When a homeserver originates a channel event, it **MUST** deliver it to
every other homeserver currently participating in the channel. Delivery
**SHOULD** be retried with backoff if a destination homeserver is
unreachable.

## Backfill

When a homeserver newly joins a channel (one of its users is added as a
member), it **MUST** backfill: request the existing event graph (or the
portion needed) from a homeserver already participating in the channel.

Backfill applies to **messages and channel state only**. Media referenced
by those messages is explicitly **not** backfilled — see
[Media Repository](./media-repository.md).

## Verification

Every event received via direct federation or backfill **MUST** be
verified against its `origin` homeserver's published signing key (see
[Identity & Key Management](./identity.md)) before being accepted into the
local replica.

> **Open issue:** conflict resolution / state-resolution algorithm for
> concurrent events (e.g. two homeservers changing channel state at the
> same time) is not yet specified.
