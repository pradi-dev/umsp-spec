# UMSP — Unified Messaging & Social Protocol

A specification for a federated protocol that unifies direct messaging,
Discord/Slack-style servers with text/voice/video channels, LINE/WeChat-style
rich chat, and Twitter/Instagram/Facebook-style social feeds — hostable
by anyone, addressed like email (`username@host.tld`).

This repository contains the spec itself, built with
[mdBook](https://github.com/rust-lang/mdBook) and published via GitHub
Pages.

## Reading the spec

Once published: `https://pradi-dev.github.io/umsp-spec/`

## Building locally

```sh
mdbook serve
```

Then open `http://localhost:3000`.

## Structure

- `src/architecture/` — Part I: approachable explanation of the design
  and its rationale.
- `src/protocol/` — Part II: normative protocol specification.
- `src/appendices/` — glossary and reference material.

## Status

Draft, work in progress.
