# Encryption Envelope

DMs **MAY** be end-to-end encrypted. UMSP does not mandate a single
cryptosystem; instead it defines one minimal, mandatory **envelope**
shape so that every implementation agrees on how to recognize and carry
encrypted content, regardless of which underlying algorithm produced it.

## Envelope shape

When an event is encrypted, its `content` field (see
[Event Format](./event-format.md)) **MUST** be entirely replaced by:

```json
{
  "algorithm": "umsp.e2ee.v1-x25519",
  "ciphertext": "base64..."
}
```

- `algorithm` identifies the encryption scheme used, and **MUST** be
  present so implementations that don't support it can safely leave the
  content opaque rather than misinterpreting it.
- `ciphertext` **MUST** contain no recoverable plaintext outside of it —
  a homeserver or non-participating client holding this event learns
  nothing about the message content.

This is a deliberate difference from a "bolt-on" approach such as adding
an extra encrypted field in the generic `attributes` object (see
[Event Format](./event-format.md)) alongside plaintext content: that would
leave plaintext readable by the homeserver and defeat the purpose
entirely. Encryption **MUST** replace `content`, not sit next to it.

Clients that do not support the given `algorithm` **MUST** display the
event as an unreadable/encrypted placeholder rather than attempting to
parse `ciphertext`.

> **Open issue:** the specific default algorithm(s) to register (e.g. a
> Signal/Double-Ratchet-based scheme, key-exchange and multi-device
> semantics) are not yet specified — this section only fixes the wire
> shape.
