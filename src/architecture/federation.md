# Federation Model

## Channels have no single owner server

In a Discord/Slack-style server with members from different homeservers, no
single homeserver "hosts" a channel. Instead, **every homeserver that has
at least one member in a channel keeps its own full replica** of that
channel's message history and state — an event graph (DAG), where each
event is cryptographically signed by the homeserver that produced it (see
[Security & Trust Model](./security.md)).

When a user posts a message, their homeserver federates that event to
every other homeserver participating in the channel, which store it in
their own replica. When a new homeserver joins a channel (one of its users
is invited), it **backfills**: it requests the channel's history from a
homeserver already participating.

This is the same approach Matrix uses for rooms, and it exists for the
same reason: no single server being a required point of failure for a
shared channel to keep working.

**Trade-off, accepted deliberately:** storage and bandwidth for a
channel's message history is duplicated across every participating
homeserver, rather than living in one place. This is the cost of not
having a single owning server.

## Media is not backfilled

Unlike messages, media (photos, videos, files) attached to those messages
is **not** part of the replicated history. A message event only carries a
reference to the media (a content hash / URL), not the bytes. See
[Media Handling](./media.md) for the full model — in short: **pull on
first request, cache after that**, per homeserver, with no backfill.

## Server-to-server trust

Because events are federated (and can arrive indirectly, e.g. via
backfill from a third server rather than a direct connection from the
origin), authenticity cannot rely on "which IP connected to me right now"
— the same reason SPF alone isn't sufficient (SPF only validates a
direct, single-hop connection). Every event is signed by its origin
homeserver's key, discovered via DNS — see
[Security & Trust Model](./security.md) and
[Identity & Key Management](../protocol/identity.md).
