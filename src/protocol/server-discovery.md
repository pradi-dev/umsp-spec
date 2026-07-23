# Server Discovery

Given an address `username@host.tld`, a client or homeserver **MUST**
resolve the actual UMSP endpoint (address and port) for `host.tld` via a
DNS `SRV` record, rather than assuming the API is served from
`https://host.tld/` directly. This mirrors how mail exchangers are
discovered via `MX` records, and is consistent with the DNS-based trust
model in [Identity & Key Management](./identity.md) — resolution does not
depend on a webserver running at the apex domain.

## Record format

```
_umsp._tcp.host.tld.  SRV  <priority> <weight> <port> <target>
```

Example:

```
_umsp._tcp.example.tld.  86400  IN  SRV  10 0 8443 umsp1.example-hosting.tld.
```

Resolution procedure:

1. Query `_umsp._tcp.host.tld` for an `SRV` record.
2. If present, connect to `target:port` (over HTTPS) for all API requests
   for that homeserver.
3. If absent, a homeserver **MAY** fall back to
   `https://host.tld:443/` directly. Implementations **SHOULD** treat this
   fallback as deprecated once SRV adoption is established.

> **Open issue:** exact API path prefix / versioning scheme to use once
> connected (e.g. `/umsp/v1/...`) is not yet finalized.
