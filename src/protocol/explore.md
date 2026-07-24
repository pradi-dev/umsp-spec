# Explore & Aggregators

Explore/discovery content is served by independently-run **aggregator**
services, not by homeservers directly — see
[Feeds & Discovery](../architecture/feeds.md) for the rationale (no single
homeserver, and certainly no phone client, can feasibly crawl and rank the
entire network).

## Firehose (homeserver side, optional)

A homeserver **MAY** expose a firehose of its own public posts for
aggregators to consume:

```
GET https://<homeserver>/umsp/feeds/firehose
```

This endpoint is **OPTIONAL** — a homeserver that doesn't implement it is
simply not discoverable via aggregators, exactly as valid as a website
choosing not to be indexed by a search engine.

A homeserver **MUST NOT** include posts from a private profile
(see [Private profiles and follow requests](../architecture/feeds.md#private-profiles-and-follow-requests))
on this endpoint, regardless of whether the request is authenticated.

## Aggregator API (client side)

A client queries an aggregator of its choice — configured client-side,
the same way a browser has a configurable, swappable list of search
engines — for a fully ordered, paginated explore feed:

```
GET https://<aggregator>/explore?cursor=<opaque-cursor>
```

The aggregator **MUST** return results in its own chosen order — ordering
is decided entirely server-side by the aggregator. There is no
client-side re-ranking step; the client is only responsible for rendering
the page and requesting the next page via the returned cursor
(infinite-scroll pagination).

No aggregator is mandated by this specification. Multiple aggregators,
run by different parties (including a homeserver operator's own), may
coexist and compete.

> **Open issue:** firehose event shape, aggregator response schema, and
> pagination cursor format are not yet specified.
