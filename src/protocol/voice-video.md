# Voice & Video Signaling

Live audio/video in channels and calls **MUST** use WebRTC for the media
itself (SRTP over DTLS/UDP) — never HTTP or WebSocket, both of which add
too much latency overhead for live media (see
[Transport Layer](./transport.md)).

The control-plane WebSocket connection (see [Transport Layer](./transport.md))
carries only **call signaling** events, used to set up the WebRTC session:

- SDP offer / answer exchange
- ICE candidate exchange
- Call/channel join and leave notifications

Once a WebRTC session is established between participants (directly, or
via a relay/SFU where required by NAT topology), media flows over that
session, entirely outside the JSON event system used elsewhere in UMSP.

> **Open issue:** exact signaling event types/shapes, and the model for
> multi-party channel calls (mesh vs. SFU) are not yet specified.
