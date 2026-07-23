# Conventions

## Requirement keywords

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL
NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and
**OPTIONAL** in this document are to be interpreted as described in
[RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

## Terminology

- **Homeserver** — a server implementing UMSP that hosts one or more user
  accounts under a given `host.tld`.
- **Client** — any application (mobile, desktop, web, CLI) acting on
  behalf of a user, talking to that user's homeserver.
- **Channel** — a text or voice/video communication space with a defined
  membership, replicated across every homeserver with a member in it (see
  [Channel Federation](./channel-federation.md)).
- **Feed** — a profile timeline with followers, distinct from a channel
  (see [Feeds Subsystem](./feeds.md)).
- **Aggregator** — an optional, independently-run service that indexes
  public posts across many homeservers to power discovery/explore (see
  [Explore & Aggregators](./explore.md)).
- **Directory** — an optional, independently-run registry of known
  homeservers (see [Directory Registration](./directory.md)).

## Status of this specification

This is a **draft**. Sections in Part II that do not yet specify a
concrete wire format should be treated as non-normative placeholders until
revised.
