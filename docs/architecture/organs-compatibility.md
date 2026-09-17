# Organs Compatibility Contract

**Status:** Foundational
**Version:** 1.0.0
**Authority:** Defines how Android Dojo components conform to the established Organs communication spine.

This document records the communication contract extracted from the user's Organs reference on 2026-09-17.

The purpose is compatibility, not reinvention. Where the established Organs contract already solves a problem, Android Dojo uses that contract rather than creating a parallel mechanism.

## 1. The Communication Spine

```text
Registry
  │
  ├── service discovery
  └── liveness / heartbeat

Organs
  │
  ├── common HTTP surface
  ├── health / info
  ├── error envelope
  ├── correlation
  └── safety / risk gate

Communications BUS
  │
  ├── publish
  ├── consume
  ├── persistent consumer cursors
  └── dispatch

Telemetry
  │
  └── best-effort observation
```

These are separate layers with separate responsibilities.

## 2. Standard Organ Surface

Compatible organs use the established shared `create_organ_app(...)` pattern and expose:

- `/health`
- `/info`
- standard JSON errors in the form:

```json
{
  "error": {
    "code": "...",
    "message": "..."
  }
}
```

Mutating and risky operations pass the shared Critic risk gate unless explicitly exempted by the established infrastructure rules. GET, HEAD, and OPTIONS are exempt by convention.

Executive-approved operations use `X-Executive-Approved` where required.

## 3. Registry

The Registry is the service-discovery authority.

Environment variables defined by the established implementation include:

```text
ORGAN_REGISTRY_URL
ORGAN_HEARTBEAT_INTERVAL
```

The default Registry URL is `http://localhost:8000` and the default heartbeat interval is 10 seconds in the reference implementation.

Organ-to-organ URLs must not be hardcoded when the service can be discovered through Registry.

Registration provides a name, base URL, version, and capabilities. Discovery must reject stale or non-live registrations.

Blocking registration work must not block an async event loop; the established implementation moves such work to a worker thread.

## 4. Correlation

Request-scoped correlation uses a shared `ContextVar` and the HTTP header:

```text
X-Correlation-ID
```

The established shared interface provides:

- `get_correlation_id()`
- `set_correlation_id(value)`
- `reset_correlation_id(token)`
- `correlation_headers(headers=None)`

A caller-supplied correlation ID is preserved. Errors return the correlation ID so failures can be traced through the system.

## 5. Communications BUS

The Communications organ is the event and dispatch layer.

The reference implementation stores events in SQLite. The data directory is configurable through `COMM_DATA_DIR`.

An event contains:

```text
id
timestamp
topic
event_type
publisher
JSON payload
```

### Publish

```http
POST /bus/topics/{topic}/publish
```

Body:

```json
{
  "event_type": "...",
  "payload": {},
  "publisher": "..."
}
```

The returned event contains its assigned ID, timestamp, topic, event type, publisher, and payload.

### Peek

```http
GET /bus/topics/{topic}/events?since_id=0&limit=50
```

Peeking never advances a consumer cursor.

### Consume

```http
GET /bus/consume?consumer=NAME&topics=a,b&limit=50
```

Consumption advances that consumer's cursor for the requested topics.

Consumption is broadcast/pub-sub semantics: consuming an event does not remove it for other consumers.

Events returned across multiple topics are ordered by global event ID.

The response includes:

```json
{
  "events": [],
  "count": 0,
  "has_more": false
}
```

### Consumer State

Consumers have independent persistent cursors.

```http
GET /bus/consumers
```

A cursor can be rewound for replay or debugging:

```http
POST /bus/consumers/{consumer}/topics/{topic}/reset
```

Body:

```json
{
  "to_id": 0
}
```

### Dispatch

```http
POST /bus/dispatch
```

Body:

```json
{
  "dispatch_key": "...",
  "candidates": [],
  "strategy": "round_robin"
}
```

Established strategies are:

- `round_robin`
- `random`
- `broadcast`

Round-robin state persists by `dispatch_key`. Candidate lists may change between calls.

## 6. Telemetry

Telemetry is discovered through Registry rather than hardcoded to a service address.

Telemetry is **best effort**. Failure to emit telemetry must not make a successful operation fail.

Telemetry records may carry:

- event type
- source
- actor
- correlation ID
- parent event ID
- severity
- payload
- result
- duration
- status
- provenance

The established internal telemetry header is:

```text
X-Telemetry-Internal: 1
```

It prevents recursive telemetry noise.

Telemetry is not the safety gate and must not be used as one.

## 7. Building a Dojo Organ

A compatible component follows this sequence:

1. Import the established common organ application machinery.
2. Import the established health-check and error types.
3. Import Registry attachment/discovery support.
4. Declare a stable organ name, version, description, and capabilities.
5. Provide its own base URL for registration.
6. Create the standard organ application.
7. Attach the organ to Registry and maintain its heartbeat.
8. Discover other services by Registry name.
9. Preserve `X-Correlation-ID` across calls.
10. Use the Communications BUS for inter-component events and dispatch.
11. Keep telemetry best-effort and separate from safety/risk authorization.
12. Match established JSON/error conventions.

## 8. Dojo-Specific Rules

Android Dojo may define additional contracts for Android-specific concerns, but those contracts must sit **above** the Organs communication spine rather than replacing it.

For example, a future device-inspection organ may define a device-state event schema. That schema travels through the established BUS; it does not require a new Dojo message protocol.

Likewise, a future lab organ may define experiment results, recovery events, or artifact records. Those are domain contracts, not replacements for Registry, BUS, correlation, telemetry, or the common HTTP surface.

## 9. Host and Target Separation

The Dojo host and an Android target are separate systems.

A host-side organ may inspect or control a target through an explicit tool interface, but target state must never be inferred from host state.

Target identity belongs in domain records and relevant events. At minimum, device-specific work should preserve the Canon device identity fields:

```yaml
device:
  manufacturer: "..."
  model: "..."
  variant: "..."
  codename: "..."
  android_version: "..."
  build: "..."
  bootloader_state: "..."
  slot: "..."
```

## 10. Compatibility Rule

When implementing a new Dojo component, first ask:

> Does Organs already define this communication behavior?

If yes, use the established behavior.

If no, define the smallest Dojo-specific contract necessary, document it, version it, and keep it above the existing communication spine.

Do not silently fork the protocol.

## 11. Source Boundary

This document describes the Organs communication protocol reference supplied for Android Dojo development on 2026-09-17.

It is not authorization to access or modify any existing repository. Android-Dojo is the working repository for this project; unrelated repositories remain out of scope.
