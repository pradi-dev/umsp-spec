# Authentication

UMSP is deliberately **auth-agnostic**: the specification does not
mandate *how* a homeserver authenticates its own users (password, 2FA,
passkey, SSO, magic link) — that choice is left entirely to the
homeserver operator, the same way any two mail providers can each choose
their own login mechanism.

What UMSP *does* standardize is the shape of the flow and its result,
following the OAuth 2.0 Authorization Code model:

1. The client directs the user to the homeserver's own login page
   (rendered/controlled entirely by that homeserver).
2. The homeserver authenticates the user however it chooses.
3. On success, the flow **MUST** result in an access token handed back to
   the client.
4. When a token expires, the client repeats the flow to obtain a new one.

## Delivering the token to native/local clients

A plain redirect-based web flow does not work for a local application
without a reachable callback URL. Implementations **MUST** support at
least one of the following, per
[RFC 8252 (OAuth 2.0 for Native Apps)](https://www.rfc-editor.org/rfc/rfc8252):

- **Loopback redirect** — the client temporarily binds a local HTTP
  listener on `127.0.0.1:<port>`, opens the system browser to the login
  page with that address as the redirect target, and captures the token
  when the browser redirects back to it after login.
- **Custom URL scheme** — the client registers a scheme with the OS (e.g.
  `umsp-app://callback`); the login page redirects there after
  authentication.
- **Device Authorization Grant** — per
  [RFC 8628](https://www.rfc-editor.org/rfc/rfc8628), for clients with
  limited input (CLI, TV): the client displays a short code, the user
  authenticates in a separate browser session on any device and enters
  the code, while the client polls until authorization completes. This
  requires no redirect capability at all.

> **Open issue:** exact token format/claims and refresh semantics are not
> yet finalized.
