# Initial System Architecture

> Status: proposal. Confirm details against the actual AzerothCore checkout before implementation.

## Goals

- Reuse proven server-side world, entity, AI and persistence capabilities where technically appropriate.
- Provide a new Unreal Engine 5.5 desktop client.
- Introduce a documented client protocol boundary.
- Keep changes incremental and reversible.

## High-level components

```text
+---------------------------+
| Unreal Engine 5.5 Client  |
| UI / input / rendering     |
+-------------+-------------+
              |
       Custom TCP protocol
              |
+-------------v-------------+
| Gateway                   |
| version / auth / sessions |
| message validation        |
+-------------+-------------+
              |
       Internal adapter
              |
+-------------v-------------+
| AzerothCore adaptation    |
| world / entities / AI     |
| combat / items / scripts  |
+-------------+-------------+
              |
+-------------v-------------+
| MySQL / character data    |
+---------------------------+
```

## Component responsibilities

### Unreal client

- Rendering, input, UI, audio and local presentation.
- Client-side prediction may be explored later, but the server remains authoritative.
- Never trust client-reported damage, drops, inventory or position as final truth.

### Gateway

- Protocol version negotiation.
- Connection/session lifecycle.
- Authentication handoff and message validation.
- Rate limits, message-size limits and structured logging.
- Translation between the public client protocol and internal server interfaces.

The first prototype should be a separate process using TCP. It should only implement a version handshake and harmless test messages. It must not pretend that login or gameplay is implemented.

### AzerothCore adaptation

- Preserve upstream core behavior where possible.
- Prefer isolated modules and explicit adapter interfaces.
- Verify all hooks and APIs against the exact checked-out commit.
- Do not make broad core changes before the source audit.

### Database

- Use versioned migrations for schema changes.
- Back up and test against a non-production database first.
- Never commit production credentials or database dumps.

## Asset pipeline

AzerothCore server-side `maps`, `vmaps` and `mmaps` support server logic and navigation; they are not a complete Unreal rendering asset set.

A separate pipeline may be required for legally usable terrain, meshes, textures, skeletons, animations, collision and coordinate conversion. Do not put copyrighted game client assets in this public repository without explicit redistribution rights.

## Security principles

- Authenticate sessions before accepting gameplay messages.
- Validate message type, length, version and state transitions.
- Apply per-connection rate limits.
- Keep authoritative gameplay decisions on the server.
- Avoid logging credentials, tokens or sensitive player data.

## Decisions still to validate

- Exact AzerothCore commit and enabled modules.
- Which internal APIs can safely support the adapter.
- Whether the gateway communicates through an in-process module, a local IPC interface or another explicit interface.
- Authentication and account mapping strategy.
- World-coordinate and asset conversion strategy.
- Expected concurrency and deployment topology.
