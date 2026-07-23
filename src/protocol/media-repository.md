# Media Repository

Every homeserver **MUST** expose an HTTP media repository for uploading
and downloading media referenced by events (see
[Media Handling](../architecture/media.md) for the rationale).

## Upload

A client **MUST** upload media to its own homeserver before referencing
it in an event. The homeserver returns a content reference (a hash-based
identifier) that the client embeds in the event (e.g. as
`thumbnail-hash` in a [message attachment](./messaging.md)).

## Download: pull-then-cache

```
GET https://<homeserver>/umsp/media/<content-hash>
```

- A homeserver **MUST** serve media it originated directly.
- A homeserver **MUST**, on receiving a request for media it does not yet
  have locally but which is referenced in an event it holds, fetch it
  from the origin homeserver on first request, then cache it for
  subsequent requests from its own users.
- Homeservers **MUST NOT** backfill media proactively — only fetch
  on-demand.
- Responses **SHOULD** support `Range` requests to allow streaming/seeking
  of video without downloading the full file.

> **Open issue:** cache eviction policy, thumbnail generation, and max
> upload size are not yet specified.
