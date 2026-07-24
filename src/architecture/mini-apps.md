# Mini-Apps

Inspired by the LINE/WeChat mini-app model — small interactive web apps
launched directly from within a conversation — UMSP defines a
lightweight, federation-friendly version of the same idea.

## Why LINE's model, not WeChat's

The two existing mini-app platforms represent opposite ends of a
spectrum:

- **WeChat Mini Programs** require a custom sandboxed language/runtime,
  and every mini-program is reviewed and hosted centrally by Tencent.
  This requires a single central authority — fundamentally incompatible
  with "anyone can run their own homeserver."
- **LINE Mini Apps** are just ordinary web apps, hosted by the developer
  themselves, that include an SDK (LIFF) for identity and messaging
  integration. No central review, no custom language.

UMSP follows LINE's shape: **a mini-app is a normal, self-hosted web app**
plus a small SDK contract that any UMSP client implements. This mirrors
how [link-preview attachments](./overview.md#rich-messages) already work
— a reference to something the developer hosts, not something UMSP hosts
for them — just interactive instead of passive.

## Launching a mini-app

A mini-app is shared into a conversation as a message attachment
referencing a manifest URL. The manifest declares a display size —
**compact, tall, or full** (taken directly from LINE's UX pattern: how
much of the screen the mini-app's webview covers, from a small overlay
for quick actions up to a full-screen takeover) — and the permission
scopes it needs (e.g. reading the user's display name, posting a message
back into the conversation).

## Trust without central review

Since no central authority reviews mini-apps, trust is established the
same way a browser handles a website asking for camera or location
access: the client shows the user exactly which scopes are being
requested, anchored to the mini-app's own domain, and the user
approves or denies per-launch. There is no blanket "installed and
trusted forever" state.

## Identity: two tokens, reusing what already exists

When a user approves, their homeserver issues two tokens — deliberately
reusing infrastructure defined elsewhere rather than inventing new
machinery:

- An **ID token**, a JWT signed with the homeserver's existing signing
  key (see [Identity & Key Management](../protocol/identity.md)) — proof
  of who the user is.
- A scoped **access token** (see [Authentication](../protocol/authentication.md))
  — proof of what the mini-app is allowed to do on the user's behalf,
  limited to only the approved scopes.

The mini-app's own backend **MUST** independently verify the ID token by
resolving the issuing homeserver's public key via DNS — the exact same
verification procedure already used for
[channel-federation events](../protocol/channel-federation.md). It must
never trust identity claims the frontend has already decoded, since a
compromised or modified frontend could misreport them.

## Writing results back into the conversation

If a mini-app wants to post a result back into the chat (e.g. an order
confirmation), its backend simply calls the existing
[message-send API](../protocol/messaging.md) using its access token,
scoped to that one conversation. This needs no dedicated push/webhook
service — unlike LINE, which built a separate Service Message API for
exactly this — because UMSP already has a message-send endpoint every
client uses.

See [Mini-Apps (protocol)](../protocol/mini-apps.md) for the normative
detail.
