# Identity & Key Management

Every homeserver **MUST** have at least one signing keypair, used to sign
every federated event it originates (see
[Channel Federation](./channel-federation.md)). Ed25519 is the assumed
default algorithm.

## Publishing the public key

The public key **MUST** be published as a DNS TXT record, following the
same pattern as DKIM (`<selector>._domainkey.<domain>`), rather than via a
`.well-known` HTTP path — see
[Security & Trust Model](../architecture/security.md) for the rationale
(DNS-only bootstrapping, no dependency on a running webserver at the apex
domain).

```
<selector>._umsp-key.host.tld.  TXT  "v=umsp1; k=ed25519; p=<base64-public-key>"
```

Example:

```
2026a._umsp-key.example.tld.  TXT  "v=umsp1; k=ed25519; p=MCowBQYDK2VwAyEA..."
```

- `v` — record version, currently `umsp1`.
- `k` — key algorithm.
- `p` — base64-encoded public key.

## Selectors and key rotation

The `<selector>` label allows multiple keys to be valid simultaneously
(directly borrowed from DKIM), so a homeserver can introduce a new signing
key and phase out an old one without invalidating already-signed history.
Every signed event **MUST** reference the selector used, so verifiers know
which TXT record to fetch.

> **Open issue:** exact event field name carrying `(selector, signature)`,
> and revocation semantics for a compromised key, are not yet finalized.
