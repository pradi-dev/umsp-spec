# Directory Registration

A directory is an independently-run, **OPTIONAL** registry of known
public homeservers — separate from [aggregators](./explore.md), which
index content rather than servers. Registration is voluntary, the same
way a website is voluntarily submitted to a search engine.

## Registration (sketch)

```
POST https://<directory>/register
{
  "host": "host.tld"
}
```

A directory **SHOULD** verify a registering homeserver by resolving its
signing key (see [Identity & Key Management](./identity.md)) before
listing it, to avoid directories being trivially filled with unowned or
spoofed domains.

## Client configuration

Clients **MUST** treat the set of directories they query as a
user-configurable list — never hardcoded to a single provider — the same
way a browser lets a user choose or add search engines.

No directory is required for a homeserver to function; this endpoint
family exists purely to improve discoverability.

> **Open issue:** directory listing/query API (search by name, category,
> etc.) is not yet specified.
