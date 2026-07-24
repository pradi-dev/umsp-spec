# Feeds Subsystem

See [Feeds & Discovery](../architecture/feeds.md) for the rationale behind
treating feeds as a subsystem separate from channels.

## Posts

A feed post is an event typed `umsp.feed.post`, owned by the account that
authored it. Unlike channel events, it is not replicated into a shared
DAG — it lives on the author's homeserver and is delivered outward to
followers' homeservers.

## Following

Following is a request/response exchange between homeservers, typed
`umsp.feed.follow-request`:

```json
{
  "type": "umsp.feed.follow-request",
  "sender": "bob@host-b.tld",
  "content": { "target": "alice@host-a.tld" }
}
```

- If `alice`'s profile is **public**, `host-a.tld` **MUST** auto-accept:
  it responds with `umsp.feed.follow-accept` and adds `host-b.tld` to
  `alice`'s delivery list immediately.
- If `alice`'s profile is **private**, `host-a.tld` **MUST** hold the
  request as pending and surface it to `alice` for a manual decision. Her
  client responds with either `umsp.feed.follow-accept` (adds
  `host-b.tld` to the delivery list) or `umsp.feed.follow-reject` (no
  delivery relationship is created).

A homeserver **MUST NOT** expose a private profile's follower list, or its
posts, to unauthenticated requests or to the firehose endpoint
(see [Explore & Aggregators](./explore.md)).

## Follow delivery: shared inbox

When account `A` (on `host-a.tld`) posts, `host-a.tld` **MUST** deliver
the event to every homeserver with at least one follower of `A` on its
delivery list, via a single request per destination homeserver — a
**shared inbox** — rather than one request per follower:

```
POST https://<destination-homeserver>/umsp/feeds/inbox
```

The destination homeserver **MUST** then fan the event out internally to
all of its own local followers of `A`. This bounds outbound delivery cost
by the number of *homeservers* with at least one follower, not the number
of followers — see [Feeds & Discovery](../architecture/feeds.md) for why
(the same optimization SMTP has supported since RFC 5321 via multiple
`RCPT TO` per `DATA`).

Delivery **SHOULD** be queued asynchronously with retry/backoff for
unreachable destination homeservers.

## Explore

Explore/discovery is **not** served by the homeserver itself — see
[Explore & Aggregators](./explore.md).

> **Open issue:** unfollow/block propagation and post edit/delete
> semantics are not yet specified.
