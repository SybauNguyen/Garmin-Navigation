# A4 — Garmin Synchronization Protocol

**Document ID:** GN-A4-002 · **Protocol proposal:** GN-NAV-2 · **Document version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Specification only; exact device uplink is an open release gate.

## 1. Goals and non-goals

Transfer a compact, validated navigation route from the Flutter phone app to one selected Garmin Connect IQ app. Support integrity checking, duplicate-safe chunking, resumable transfer where storage permits and atomic route replacement.

The protocol does not transfer basemap tiles, POI catalogs, photos, discovery lists, traffic feeds or raw GPS traces. It is not a general-purpose phone-to-watch file transfer protocol.

## 2. Transport model

- Phone to watch: Garmin Connect IQ Mobile SDK app-message path, subject to exact SDK/platform setup.
- Watch to phone: `Toybox.Communications.transmit()` is **conditional** for fēnix 6S. Current Garmin API documentation labels the method Since API 4.0.4 but also lists fēnix 6S/6S Pro in the method's supported devices; the compatible-device matrix lists 6S at API 3.4. This documentation mismatch must be resolved by compile and physical delivery tests.
- Target exactly one selected device/app. A bridge that broadcasts to all connected watches is not acceptable for production.
- A phone-side SDK send-success callback is not proof that the watch accepted or committed the route.

## 3. Protocol primitives

| Primitive | Rule |
|---|---|
| Protocol identifier | `GN-NAV-2` |
| Envelope | Versioned Dictionary/Array/String/Number/Long/Boolean values supported by target SDK; do not assume ByteArray transport on 6S |
| Route encoding | Deterministic ASCII canonical stream; geometry Polyline6; numeric maneuver codes |
| Non-ASCII text | UTF-8 bytes encoded Base64URL; reassemble bytes before strict UTF-8 decode |
| Chunk checksum | CRC-32/ISO-HDLC over exact chunk bytes |
| Whole-route digest | SHA-256 over canonical route stream |
| Initial chunk experiment | 128 ASCII data bytes; test candidate only, not a Garmin hard limit |
| Window | Stop-and-wait, window size 1 until measured reliability permits change |
| Watch storage block | Conservative bounded blocks; 16 KB is an initial design target, not guaranteed quota |
| Identity | transferId + routeId + revision + targetDeviceId |

## 4. Envelope (logical schema)

Every message carries:
- `protocol`: protocol identifier;
- `messageType`: HELLO, HELLO_ACK, ROUTE_BEGIN, ROUTE_CHUNK, CHUNK_ACK, CHUNK_NACK, ROUTE_READY, ROUTE_COMMIT, ROUTE_COMMITTED, ROUTE_ABORT, NAV_COMMAND, NAV_EVENT, ERROR;
- `messageId`: unique message identity;
- `transferId`: transfer scope, nullable for non-transfer messages;
- `routeId` and `routeRevision`: exact immutable route identity where relevant;
- `sequence` and `chunkCount`: chunk ordering where relevant;
- `payloadEncoding` and `payload`: bounded content;
- `crc32`: per-chunk or message integrity where relevant;
- `capabilities`: explicit verified capability flags during negotiation.

Unknown major protocol versions are rejected. Unknown optional minor fields may be ignored only where the contract marks them optional.

## 5. Route stream projection

The route stream contains only:
1. route ID/revision and schema version;
2. selected travel mode/profile identifier;
3. calculation/source reference sufficient for diagnostics, not a provider response dump;
4. ordered geometry encoded as Polyline6;
5. ordered maneuver records with canonical numeric maneuver type, shape index/route offset, and compact instruction text;
6. stop/arrival information;
7. essential route warnings and validity fields.

No map tiles, discovery POIs, ratings/photos, user search history or raw provider payload is allowed in the watch stream.

## 6. Transfer state machine

`IDLE → NEGOTIATING → RECEIVING → VALIDATING → READY_TO_COMMIT → COMMITTING → COMMITTED`

Failure/terminal paths: `RETRY_WAIT`, `ABORTED`, `REJECTED`, `EXPIRED`.

Rules:
- `ROUTE_BEGIN` declares route identity, stream length, chunk count, encoding and whole-route digest.
- Each chunk is written to a pending/staging slot and checked for transfer identity, sequence, duplicate status, length and CRC.
- A chunk ACK is sent only after validation and durable staging write.
- Duplicate already-durable chunks are idempotent; conflicting duplicate bytes reject the transfer.
- Missing/out-of-order chunks cause a bounded NACK/retry or resume request.
- After all chunks arrive, the watch reassembles the canonical stream, checks strict encoding, schema, route geometry/maneuvers and SHA-256 digest.
- The watch reports READY only after complete validation and durable staging.
- The phone sends COMMIT for that exact transfer/route revision.
- The watch switches the active pointer only after commit data is durably written; old active route remains recoverable until this step.
- A failed transfer never destroys the previously committed route.

## 7. Navigation messages

Commands: START, PAUSE, RESUME, STOP, REQUEST_STATUS, DELETE_PENDING.

Events: WATCH_READY, NAV_STARTED, NAV_PAUSED, NAV_RESUMED, OFF_ROUTE, REJOINED, ARRIVED, GPS_UNAVAILABLE, STORAGE_ERROR, NAV_ERROR, NAV_STATUS.

Every event is scoped to sessionId + routeId/revision and includes event time/state version. Stale or duplicated events are idempotently ignored. Watch-originated events are not advertised until uplink is verified.

## 8. ACK and fallback policy

### Verified two-way mode
Application-level ACK/READY/COMMITTED and watch-originated events are enabled only after:
1. Monkey C compile succeeds at the selected minimum API;
2. `transmit()` executes on exact model/firmware;
3. a phone listener receives payloads over the supported Android/iOS configuration;
4. delivery/retry and disconnect/reconnect tests pass.

### Uplink not verified
- Do not display “Synced”, “Confirmed” or “Committed”.
- Show “Sent — confirmation unavailable” only if the phone SDK actually reports a send attempt.
- Preserve the prior active route.
- Disable claims of autonomous new-route navigation and watch-to-phone off-route reporting.
- A phone-led guidance relay can be prototyped as a reduced fallback, but it is not equivalent to an independently cached watch route and must be separately specified/tested.

## 9. Security and resilience

- Reject oversized fields/chunks before allocation.
- Validate all indexes, lengths, encoding, mode values and enum values.
- Never execute remote code or trust a provider instruction as a protocol command.
- Bind messages to the selected device and active transfer identity.
- Limit retries, expire pending sessions and clean orphaned staging data.
- Do not log full GPS traces or personal destination names by default.
- Route payloads must have a documented provider/license lineage before Garmin export.

## 10. A4 acceptance criteria

- A4-AT-001: protocol major mismatch is rejected with a controlled error.
- A4-AT-002: selected-device routing prevents delivery to non-selected watches.
- A4-AT-003: message size/route length limits are enforced before staging.
- A4-AT-004: missing/duplicate/out-of-order chunks do not corrupt the pending route.
- A4-AT-005: CRC mismatch rejects the affected chunk.
- A4-AT-006: whole-route SHA mismatch rejects the entire transfer.
- A4-AT-007: ACK occurs only after durable staging.
- A4-AT-008: conflicting duplicate chunk is rejected.
- A4-AT-009: interruption/reconnect either resumes safely or aborts safely.
- A4-AT-010: incomplete or corrupt replacement preserves the active route.
- A4-AT-011: route commit is atomic across restart/power-loss simulation.
- A4-AT-012: unsupported uplink is not advertised as available.
- A4-AT-013: old revision events cannot mutate the new active session.
- A4-AT-014: the canonical payload is deterministic across repeated serialization.
- A4-AT-015: Vietnamese instruction text round-trips through UTF-8/Base64URL without corruption.

## 11. Open protocol/bench decisions

- Actual sendMessage payload size/type behavior on the chosen Android/iOS SDKs.
- `transmit()` API compatibility on fēnix 6S API 3.4.
- Real chunk size, latency, retry timing and storage write durability.
- Maximum shape points/maneuvers/route distance for the base 6S.
- Whether status and uplink can be maintained across phone/watch app lifecycle transitions.
