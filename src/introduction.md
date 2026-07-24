# Introduction

**UMSP (Unified Messaging & Social Protocol)** is a federated protocol for
communication and social interaction. It combines four things that today
live in separate, incompatible products:

- **Direct messages** — one-to-one and group conversations.
- **Discord/Slack-style servers** — communities organized into text and
  voice/video channels.
- **Rich chat messages** — LINE/WeChat-style messages that can carry
  attachments such as link previews, not just plain text.
- **Social feeds** — Twitter/Instagram/Facebook-style profile timelines,
  following, and discovery.

Like email, UMSP is **federated**: anyone — an individual or a company —
can run their own server ("homeserver"). Identity is expressed the same
way an email address is: `username@host.tld`. No single company owns the
network; interoperability comes from everyone implementing the same
specification, the same way any two mail servers on the internet can talk
to each other regardless of who wrote the software.

## How this document is organized

This specification is split into two parts, because it serves two very
different readers:

- **[Part I: Architecture & Concepts](./architecture/vision.md)** explains
  *what* the system does and *why* it is built this way. It is written to
  be approachable for anyone interested in the design, not just protocol
  implementers.
- **[Part II: Protocol Specification](./protocol/conventions.md)** is
  normative. It defines the wire format, required behavior, and exact
  rules an implementation **MUST** follow to interoperate with the rest of
  the network — in the style of an IETF RFC (see
  [Conventions](./protocol/conventions.md) for the keywords used).

## Status

This is a **draft, work-in-progress specification**. Sections are added
and revised as design decisions are made. Anything not yet marked
normative in Part II should be considered unstable.
