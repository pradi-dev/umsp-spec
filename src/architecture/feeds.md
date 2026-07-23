# Feeds & Discovery

Profile feeds (the Twitter/Instagram/Facebook-style homepage and timeline)
are a **separate subsystem** from channels, not a special case of them —
see [System Overview](./overview.md) for why. A feed can have an unbounded,
unknown number of followers on an unbounded number of homeservers, and
must additionally support discovery by people who don't follow the
account yet. Neither property holds for a channel.

## Following: push delivery

When you follow an account, your homeserver is added to the list of
servers that receive that account's posts. When the account posts, its
homeserver pushes the event out to every homeserver that has at least one
follower — this is the same inbox-delivery model ActivityPub/Mastodon
already use.

### Shared-inbox fan-out

A naive implementation would send one copy of a post per *follower*. If an
account has hundreds of thousands of followers spread across many
homeservers, that doesn't scale. Instead, a homeserver's origin server
delivers **one copy per destination homeserver**, to a shared inbox
endpoint; the receiving homeserver fans it out internally to all of its
own local followers.

This turns delivery cost from *O(followers)* into *O(homeservers with at
least one follower)* — and it isn't a novel idea: it's the same
optimization SMTP has supported since RFC 5321, where a single `DATA`
transmission can be addressed to multiple `RCPT TO` recipients on the same
destination mail server in one session. ActivityPub's "shared inbox" is a
reimplementation of the same trick over HTTP.

Delivery to homeservers happens over an asynchronous queue with
retry/backoff, again mirroring how SMTP mail queues handle temporarily
unreachable destination servers.

## Explore: aggregators, not a firehose you run yourself

The follow-feed model doesn't help with *discovering* content from
accounts you don't already follow — no homeserver can feasibly crawl the
entire network to build a global "explore" page, and a phone client
certainly can't. Two existing systems show the range of options:

- **Mastodon** never solved this well: its "explore" surface only shows
  content the local homeserver happens to already know about.
- **Bluesky (AT Protocol)** solves it with a **firehose**: relay servers
  aggregate a stream of all public posts network-wide, and separate
  "feed generator" services consume that stream to produce a feed.

UMSP follows the same shape, simplified: a homeserver *may* optionally
expose a lightweight firehose of its own public posts. Separate,
independent **aggregator services** — not a required part of running a
homeserver — subscribe to many homeservers' firehoses, and are
responsible for the heavy lifting: indexing, ranking, and serving a fully
paginated, ordered explore feed (infinite scroll) directly to clients.

Which aggregator a client queries is a free, user-facing choice —
conceptually the same as choosing a search engine in a browser — never a
single mandated service. The ordering itself is entirely decided
server-side by the chosen aggregator; there is no client-side re-ranking
step.

## Directories: opt-in discovery of servers, not content

Separately from aggregators (which index *content*), **directories** index
*servers*: a homeserver operator can voluntarily register their server
with one or more public directories, the way a website is voluntarily
submitted to a search engine. Directories are optional, may be run by
anyone, and a homeserver that isn't listed in any directory works exactly
as well for its own users — it's simply less discoverable to people who
don't already know its address.

Clients maintain a configurable list of known directories, the same way a
browser has a configurable, swappable list of search engines. See
[Directory Registration](../protocol/directory.md).
