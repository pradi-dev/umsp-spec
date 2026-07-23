# Vision & Goals

## The problem

Today, the four communication modes UMSP unifies are split across products
that cannot talk to each other: iMessage/WhatsApp for DMs, Discord for
communities, LINE/WeChat for rich chat, Instagram/Twitter/Facebook for
feeds. Each is a walled garden controlled by a single company. You cannot
run your own server, and you cannot talk to a Discord user from a WhatsApp
account any more than you could send an email from a phone that isn't
allowed to talk to Gmail.

Email proved decades ago that this doesn't have to be the case: anyone can
run a mail server, and `alice@company-a.com` can email `bob@company-b.com`
without either company needing a business relationship. UMSP applies that
same model to modern, rich, real-time communication.

## Goals

- **Federation by default.** Any individual or company can host their own
  server. Interoperability comes from a shared spec, not a shared vendor.
- **One identity, every mode of communication.** `username@host.tld`
  works for DMs, servers/channels, feeds, and voice/video — the way an
  email address today only works for mail.
- **No mandatory central authority.** Directories, feed aggregators, and
  other discovery infrastructure are optional, pluggable, and
  competition-friendly — never a required chokepoint for the protocol to
  function (see [Federation Model](./federation.md) and
  [Feeds & Discovery](./feeds.md)).
- **Learn from prior art, don't reinvent it.** Where existing federated
  systems (email/SMTP/IMAP, Matrix, ActivityPub/Mastodon, Bluesky/AT
  Protocol, XMPP) already solved a problem well, UMSP reuses the same
  pattern rather than inventing a new one. Where they got it wrong (e.g.
  SMTP's lack of built-in sender verification), UMSP builds the fix in
  from day one instead of bolting it on later.

## Non-goals

- UMSP does not try to be a single global "everything app" run by one
  company. Any client or server implementation is expected to compete on
  merit, exactly like email clients and providers do today.
- UMSP does not mandate one specific end-to-end encryption algorithm (see
  [Security & Trust Model](./security.md)) — it defines a common,
  interoperable envelope, not a single cryptosystem forever.
