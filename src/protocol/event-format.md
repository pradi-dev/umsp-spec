# Event Format

Every piece of state or activity in UMSP — a chat message, a channel
membership change, a feed post — is represented as an **event**: a JSON
object signed by its origin homeserver (see
[Identity & Key Management](./identity.md)).

## Baseline shape

```json
{
  "type": "umsp.message",
  "origin": "host.tld",
  "sender": "alice@host.tld",
  "timestamp": 1745395200,
  "content": { },
  "attributes": { }
}
```

- `type` — identifies what kind of event this is (namespaced, e.g.
  `umsp.message`, `umsp.feed.post`).
- `origin` — the homeserver whose signing key must be used to verify this
  event (see [Identity & Key Management](./identity.md)).
- `content` — the type-specific payload. For an encrypted event, `content`
  is replaced entirely by the encrypted envelope — see
  [Encryption Envelope](./encryption.md).
- `attributes` — a generic, open-ended object for client-defined
  extensions that are not part of the core spec, directly analogous to
  email's custom `X-` headers. Homeservers **MUST** pass `attributes`
  through unmodified and **MUST NOT** require understanding its contents
  to route or store the event.

> **Open issue:** the exact signature field(s), and canonicalization rules
> for computing the signature over this JSON structure, are not yet
> finalized.
