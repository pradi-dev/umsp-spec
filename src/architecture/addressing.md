# Addressing

Every UMSP identity has the form:

```
username@host.tld
```

exactly like an email address. `host.tld` identifies the homeserver that
hosts the account and is the authority for that user's identity; `username`
identifies the account on that server.

This single address is used everywhere in the system — DMs, channel
membership, feed handles, and voice/video call invites all resolve the
same way. There is no separate identity system per subsystem.

Resolving `username@host.tld` to an actual, reachable server (which may
run on different infrastructure than the website at `host.tld`) is
handled by DNS, the same way mail servers are found via `MX` records. See
[Server Discovery](../protocol/server-discovery.md) for the normative
lookup procedure.
