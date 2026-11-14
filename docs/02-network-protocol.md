# Initial Network Protocol Proposal

> Status: design draft only. This is not an implemented or security-reviewed protocol.

## Transport

Start with TCP for the first prototype because it simplifies reliable message framing and debugging. Evaluate UDP or a hybrid transport only after the basic gameplay loop works and latency requirements are measured.

Use TLS or an equivalently protected channel before exposing authentication or account credentials over an untrusted network.

## Message framing

Every message should have a fixed, documented header. Proposed fields:

| Field | Purpose |
|---|---|
| Protocol version | Reject incompatible clients |
| Message type | Identify the payload schema |
| Payload length | Bound and parse the message |
| Request/sequence ID | Correlate requests and responses where needed |

The implementation must explicitly define byte order, integer widths, maximum frame size, string encoding and malformed-frame behavior. Do not rely on compiler-specific struct layout.

## Initial message families

1. Handshake: client hello, server hello, version rejection.
2. Authentication: login request, login result, session establishment.
3. Character: character list, create, select and enter world.
4. World: initial snapshot, entity spawn/despawn and movement updates.
5. Combat: attack request, skill request, authoritative combat result.
6. Items: inventory snapshot, item changes and loot result.
7. Social: chat, party and guild messages (later phase).

Only handshake and harmless test messages belong in the first gateway prototype. The remaining message families are planning items, not implemented features.

## Authority and validation

- The client sends intent, not trusted outcomes.
- The server validates movement, range, cooldowns, resources and line of sight.
- The server calculates damage, death, drops, inventory changes and persistence.
- The server rejects messages that are invalid for the current session or character state.
- The gateway enforces frame-size limits and rejects malformed or unsupported messages.

## Session lifecycle

Proposed states:

```text
Disconnected -> Connected -> VersionChecked
             -> Authenticated -> CharacterSelected -> InWorld
```

Every transition must be validated server-side. Disconnects, timeouts and duplicate requests must be handled safely.

## Compatibility

- Version the protocol from the first message.
- Document each message schema and its version.
- Define behavior for unknown message types.
- Avoid silently reinterpreting old payloads.
- Add automated serialization and malformed-input tests.

## Open questions

- Authentication source and account mapping.
- Gateway-to-world-server interface.
- Whether messages need compression.
- Sequence numbering and replay protection.
- Reconnection and session resumption.
- Chat encoding and localization.
