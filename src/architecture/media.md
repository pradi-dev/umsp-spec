# Media Handling

Photos, videos, files, avatars, and link-preview thumbnails are served
over plain HTTP, deliberately separate from the real-time control plane
(see [System Overview](./overview.md)). HTTP is well suited to this job:
range requests (so video can seek/stream), browser and CDN caching, and
resumable uploads all come for free.

## Pull-then-cache, no backfill

A message event never carries media bytes — only a reference (a content
hash / URL pointing at the origin homeserver's media repository). A
homeserver fetches the actual bytes **the first time one of its own users
requests them**, then caches the result locally for all of its users going
forward. If a channel spans multiple homeservers, each one independently
pulls and caches the media for its own users the first time it's needed.

This is the same model Matrix uses for its content repository, chosen
deliberately over two alternatives:

- **Push at delivery time** (how SMTP/IMAP handle attachments — the
  sending server transmits the full bytes immediately, so the recipient's
  server has its own permanent copy right away): wastes bandwidth and
  storage for media nobody ever opens.
- **Always fetch directly from the origin, never cache**: puts all load
  permanently on the original sender's homeserver, and leaks who is
  viewing the media to that server on every single view.

The trade-off accepted with pull-then-cache: if the origin homeserver goes
offline *before* any recipient homeserver has done its first fetch, the
media becomes unavailable — a risk plain email attachments don't have,
since email pushes the full bytes at delivery time.

## Federation with many homeservers (fan-out)

The pull-then-cache model composes with channel federation
([Federation Model](./federation.md)): every homeserver that has a member
in the channel does its own first-fetch-then-cache independently. No
special coordination between homeservers is required for media.
