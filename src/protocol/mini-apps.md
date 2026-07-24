# Mini-Apps

See [Mini-Apps](../architecture/mini-apps.md) for the rationale behind
this design. This section specifies the mechanics.

## Manifest

A mini-app is described by a JSON manifest, hosted by its developer:

```json
{
  "name": "Example Shop",
  "icon": "https://shop.example.tld/icon.png",
  "launch_url": "https://shop.example.tld/app",
  "display": "compact",
  "scopes": ["profile", "send-message"]
}
```

- `display` **MUST** be one of `compact`, `tall`, or `full`.
- `scopes` **MUST** list every permission the mini-app requires; a client
  **MUST** show these to the user for approval before launch and **MUST
  NOT** request tokens for scopes the user did not approve.

## Attachment

A mini-app is shared into a conversation as a message attachment:

```json
{
  "kind": "mini-app",
  "manifest-url": "https://shop.example.tld/umsp-miniapp.json"
}
```

## Token issuance

On launch, after the user approves the requested scopes, the client
**MUST** request a token pair from the user's homeserver:

```http
POST /umsp/v1/miniapp/token
Authorization: Bearer <user's existing session token>
Content-Type: application/json

{
  "client_id": "shop.example.tld",
  "scopes": ["profile", "send-message"],
  "context": { "conversation_id": "channel:general@host.tld" }
}
```

Response:

```json
{
  "id_token": "<JWT>",
  "access_token": "<opaque scoped token>",
  "expires_in": 3600
}
```

The `id_token` **MUST** be a JWT signed with the homeserver's signing key
(see [Identity & Key Management](./identity.md)), with at minimum:

```json
{
  "iss": "host.tld",
  "sub": "alice@host.tld",
  "aud": "shop.example.tld",
  "exp": 1745398800,
  "scope": ["profile", "send-message"]
}
```

`aud` **MUST** be checked by verifiers and **MUST** match the mini-app's
own origin, so a token issued for one mini-app cannot be replayed against
another.

## Delivering tokens to the mini-app

A client **MUST NOT** pass tokens as URL query parameters (they leak via
browser history and `Referer` headers). Tokens **MUST** be delivered via
an in-page JS bridge the client injects into the mini-app's webview, e.g.:

```js
window.umsp.getIdToken()
window.umsp.getAccessToken()
```

## Verification (mini-app backend)

The mini-app's frontend **MUST** forward the raw `id_token` to its own
backend, which **MUST** independently verify it before trusting any
identity claim:

1. Read `kid`/issuer from the token to determine the signing selector
   (see [Identity & Key Management](./identity.md)).
2. Resolve the homeserver's public key via its DNS TXT record.
3. Verify the signature, and check `aud` matches this mini-app and `exp`
   has not passed.

A backend **MUST NOT** trust identity data the frontend has already
decoded from the token — only the verified result of this procedure.

## Writing back to the conversation

To post a result into the conversation, the mini-app's backend calls the
existing message-send endpoint using its `access_token`:

```http
POST /umsp/v1/messages
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "conversation_id": "channel:general@host.tld",
  "type": "umsp.message",
  "content": { "body": "Order #123 confirmed" }
}
```

The homeserver **MUST** reject this request with `403` if the
`access_token`'s granted scopes do not include `send-message`, or if
`conversation_id` does not match the conversation the token was scoped
to at issuance.

> **Open issue:** token revocation, refresh semantics, and additional
> scope types beyond `profile`/`send-message` are not yet specified.
