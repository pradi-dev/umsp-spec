# Security & Trust Model

## Why event signing, not just SPF/TLS

Because channel history is federated and can reach a homeserver
indirectly — via backfill from a third homeserver, not necessarily a
direct connection from the event's origin (see
[Federation Model](./federation.md)) — trust cannot be based on "which IP
just connected to me." That is exactly the limitation SPF has for email:
SPF only validates the server on the *other end of the current
connection*, which says nothing when content is relayed through
intermediaries, and says nothing about whether the content itself was
tampered with in transit.

Email's actual answer to *content* authenticity is DKIM: a cryptographic
signature over the message, with the public key published in DNS. UMSP
takes the same approach from the start, rather than retrofitting it after
abuse forces the issue (as happened with email — see
[History of email spam](https://handwiki.org/wiki/History_of_email_spam),
[Email spoofing](https://en.wikipedia.org/wiki/Email_spoofing)).

## Per-homeserver signing keys via DNS

Every homeserver has a signing keypair. The public key is published as a
DNS TXT record, DKIM-style, not via a `.well-known` HTTP file — this
avoids bootstrapping trust through a webserver that might not even be the
thing running the actual UMSP service. See
[Identity & Key Management](../protocol/identity.md) for the exact record
format, including selector-based key rotation (also borrowed from DKIM).

Every federated event is signed by its origin homeserver's key. Any
receiving homeserver can verify authenticity and integrity independent of
how many hops the event traveled through to reach it.

## End-to-end encryption

DMs support end-to-end encryption via a minimal, mandatory **encrypted
envelope** format: an event's content can be replaced entirely by
`{ algorithm, ciphertext }`. Homeservers and non-participating clients see
only this opaque structure — never the plaintext next to it. Which actual
cryptographic scheme is used is indicated by `algorithm` and is
deliberately extensible/pluggable, rather than the spec mandating one
fixed cryptosystem forever. See [Encryption Envelope](../protocol/encryption.md).

This is a deliberate contrast with plain email, which has no default
end-to-end encryption at all (only transport-level TLS between servers)
and where optional add-ons like PGP/S-MIME see very low real-world
adoption due to key-exchange friction.

## General extensibility

Beyond encryption specifically, every event supports a generic custom
attributes mechanism — the same idea as email's `X-` custom headers, or
Matrix's arbitrary event-content keys — so clients can build new features
without the core spec needing to anticipate every one of them.
