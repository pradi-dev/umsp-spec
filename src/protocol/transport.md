# Transport Layer

UMSP splits transport by workload rather than using a single transport for
everything (see [System Overview](../architecture/overview.md) for the
rationale behind each choice).

## Control plane: HTTP + WebSocket

Request/response operations (fetching history, profile data, channel
state) **MUST** be served over HTTPS. Real-time push (new messages,
presence, typing indicators, call signaling) **MUST** be delivered over a
WebSocket connection established after authentication
(see [Authentication](./authentication.md)).

## Media: HTTP

Media upload/download **MUST** be served over plain HTTPS by each
homeserver's own media repository, supporting standard `Range` requests
so that video can be streamed/seeked without downloading the full file.
See [Media Repository](./media-repository.md).

## Voice/video: WebRTC

Live audio/video **MUST** use WebRTC (SRTP media over DTLS/UDP). The
control plane's WebSocket connection is used only to signal call setup
(SDP offer/answer exchange, ICE candidate exchange) — never to carry the
actual media stream. See [Voice & Video Signaling](./voice-video.md).

## Payload format: JSON

All control-plane and feed payloads **MUST** be encoded as JSON. This is
chosen over a binary format (e.g. Protobuf/MessagePack) deliberately: JSON
requires no codegen tooling to implement in any language and is directly
readable in network logs, which matters more for a protocol whose entire
goal is broad, independent implementation than the marginal bandwidth
savings of a binary format would.
