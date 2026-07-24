# Federation Model

## Channels have no single owner server

In a Discord/Slack-style server with members from different homeservers, no
single homeserver "hosts" a channel. Instead, **every homeserver that has
at least one member in a channel keeps its own replica** of that
channel's message history and state — an event graph (DAG), where each
event is cryptographically signed by the homeserver that produced it (see
[Security & Trust Model](./security.md)).

When a user posts a message, their homeserver federates that event to
every other homeserver currently participating in the channel, which
store it in their own replica.

This is the same DAG-of-signed-events approach Matrix uses for rooms, and
it exists for the same reason: no single server being a required point of
failure for a shared channel to keep working.

## No backfill

Unlike Matrix, a homeserver joining a channel does **not** request the
channel's prior history from another participant. Its replica simply
begins at the moment one of its users is added as a member, and only
grows from events delivered from that point on (see
[Membership](#membership-and-ownership) below).

**Trade-off, accepted deliberately:** a member who joins later genuinely
cannot see anything from before they joined — different homeservers
participating in the same channel end up holding different-length
histories of it. This is a real loss of functionality compared to
Discord/Slack (which show full history to new members) or Matrix (which
backfills it), traded for a much simpler implementation: no history-sync
protocol, no negotiating how much history to request, nothing to build
for a homeserver whose members only ever care about a channel going
forward. Consistent with [keeping the spec simple](./vision.md#goals).

It also means channel history storage/bandwidth is only ever duplicated
across homeservers whose members actually witnessed it as it happened —
not retroactively synced to every later joiner.

## Membership and ownership

A channel has an **owner** account — by default, whoever created it —
who alone can add or remove members. Adding a member is what causes their
homeserver to start participating in delivery for that channel (from that
point forward, per [No backfill](#no-backfill) above); removing one stops
it. A user discovers which channels they currently belong to via a
dedicated endpoint. See
[Channel Federation](../protocol/channel-federation.md) for the exact
API.

## Server-to-server trust

Even though delivery is direct from an event's origin homeserver to each
participating homeserver, a live TLS connection only proves who you're
talking to *right now* — it says nothing once the event is stored and
read later, long after that connection is gone. That's why every event
carries its own persistent, independently-checkable signature rather than
relying on transport security alone — the same reason DKIM signs the
mail content itself instead of trusting SMTP's transport encryption to
vouch for it forever. Every event is signed by its origin homeserver's
key, discovered via DNS — see [Security & Trust Model](./security.md) and
[Identity & Key Management](../protocol/identity.md).
