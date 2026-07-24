# Glossary

- **Aggregator** — an independent service that indexes public posts across
  homeservers to power explore/discovery feeds. See
  [Explore & Aggregators](../protocol/explore.md).
- **Backfill** — requesting a channel's prior history when newly joining
  it. Unlike Matrix, UMSP deliberately does **not** do this — see
  [No backfill](../protocol/channel-federation.md#no-backfill).
- **Channel** — a text or voice/video communication space with a defined
  membership, replicated across every participating homeserver.
- **Directory** — an independent, opt-in registry of known homeservers.
  See [Directory Registration](../protocol/directory.md).
- **Event** — the basic unit of data in UMSP: a signed JSON object
  representing a message, feed post, or state change. See
  [Event Format](../protocol/event-format.md).
- **Feed** — a profile timeline with followers, distinct from a channel.
  See [Feeds Subsystem](../protocol/feeds.md).
- **Homeserver** — a server implementing UMSP that hosts one or more
  accounts under a `host.tld`.
- **Mini-app** — a self-hosted, interactive web app launched from within
  a conversation, authorized via a signed ID token and a scoped access
  token. See [Mini-Apps](../protocol/mini-apps.md).
- **Shared inbox** — a delivery optimization where a homeserver receives
  one copy of an event per destination homeserver rather than per
  follower, then fans it out locally.
