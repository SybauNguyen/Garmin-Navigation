# A5 — Garmin Capability & Platform Matrix

**Document ID:** GN-A5-002 · **Version:** 2.0.0 · **Date:** 2026-11-11  
**Status:** Official documentation reviewed; compile/bench/device verification still pending.

## 1. Target device family

Garmin's current Connect IQ compatible-device matrix lists both:
- fēnix 6S / 6S Solar / 6S Dual Power;
- fēnix 6S Pro / 6S Sapphire / 6S Pro Solar / 6S Pro Dual Power;

at **API Level 3.4**, 240×240 round Memory-in-Pixel display, 64 colors.

The exact user model/firmware remains unconfirmed. Design conservatively supports base 6S.

## 2. Native map support

The current `Toybox.WatchUi.MapView` supported-device list includes fēnix 6S Pro/Sapphire variants, but not the base fēnix 6S variants.

| Capability | Base fēnix 6S | 6S Pro/Sapphire | Product policy |
|---|---|---|---|
| Display | 240×240 round MIP | 240×240 round MIP | Round clipping safe layout |
| Connect IQ API | 3.4 | 3.4 | Minimum API candidate 3.4 |
| GPS / Position | Supported | Supported | Runtime/fix-quality validation |
| Phone messaging | Supported-device family | Supported-device family | Verify actual Mobile SDK path |
| MapView / MapTrackView | Not in MapView list | Listed | Native renderer only on verified Pro profile |
| Graphics custom rendering | Required | Fallback required | Base renderer must work on both |
| Haptics | Capability must be runtime/bench verified | Same | Suppress duplicate vibration |
| Storage | Application.Storage API available | Same | Bound blocks; test full condition |
| Watch→phone transmit | Documentation/API discrepancy | Same | Conditional; no assumption |

The base model baseline is a custom route renderer: route line, current-position marker, heading/bearing, simplified nearby geometry and a compact instruction HUD. It must not call an unsupported native MapView API.

## 3. Communications API discrepancy

The current Garmin `Toybox.Communications.transmit()` reference is marked **Since API Level 4.0.4**. The same method's supported-device list includes fēnix 6S and 6S Pro, while the compatible-device matrix lists those watches at API 3.4.

This is an unresolved documentation inconsistency. Do not silently infer compatibility from the supported-device list or from the Since label alone. Build a minimal compile/runtime probe with the actual Connect IQ SDK and test delivery on the exact watch/firmware and phone platform. Until then:
- mark watch-to-phone application events unavailable;
- do not advertise transfer as confirmed unless a separate tested app-level confirmation exists;
- keep a previous valid watch route intact.

ByteArray support for transmit is documented since API 6.0.0; do not use it as the 6S/API 3.4 baseline. Use only data types proven at compile/runtime for the selected API.

## 4. Storage

Garmin Application.Storage:
- is available since API 2.4.0;
- accepts bounded supported Monkey C value types;
- limits each stored value to **32 KB**;
- has an aggregate Object Store size limit that varies by device;
- can throw when storage capacity is exceeded.

Use small bounded blocks and an active/pending route slot. A 16 KB block is a conservative design target for experimentation, not a guaranteed quota. Catch storage exceptions and preserve the last committed route. Physical measurement is mandatory.

## 5. App type and lifecycle

- Use a **Watch App** for user-started navigation, not a Widget intended for short glance interactions.
- Declare only required manifest permissions, including Positioning and Communications where used.
- Treat location events as subject to app lifecycle and OS/runtime availability. No promise of indefinitely running GPS while inactive or backgrounded.
- On lost/poor GPS, freeze authoritative progress and turn countdown; show a visible stale/no-fix state.
- When app resumes, reacquire a good fix before progressing the route.

## 6. Phone integration

- Flutter companion app is the primary product.
- Garmin Connect IQ Mobile SDK is the official integration path to evaluate.
- The device/app adapter must retain exact selected-device identity.
- The inspected community `watch_connectivity_garmin` wrapper was previously observed to send to all connected watches; do not adopt it unchanged unless a selected-device targeting fix is verified.
- Pin Flutter, Dart, Android/iOS, Garmin Mobile SDK, Connect IQ SDK and package versions during A9/B. Do not rely on a package's latest version without checking the user's installed toolchain.

## 7. Device profiles and capability discovery

At runtime, construct a capability record:
- model and firmware;
- API level/build target;
- native MapView available;
- GPS support/permission/fix status;
- haptic support;
- phone messaging receive support;
- phone messaging transmit support (true only when verified);
- supported storage/encoding profile;
- maximum measured safe route payload.

Unknown model uses conservative custom-renderer mode. A capability is not declared based only on a brand/model string if the API can be queried or tested.

## 8. Physical verification plan

| ID | Test | Pass condition |
|---|---|---|
| A5-AT-001 | Build base 6S target at API 3.4 | App compiles without APIs above target |
| A5-AT-002 | Build Pro/Sapphire target | No unsupported API path |
| A5-AT-003 | Base renderer | Route/HUD legible without native MapView |
| A5-AT-004 | Pro renderer | Native map path works and custom fallback remains |
| A5-AT-005 | Position permission | Denial/revocation/no-fix handled |
| A5-AT-006 | GPS lifecycle | Leaving/reopening app does not fabricate progress |
| A5-AT-007 | transmit probe | Exact compile and watch→phone delivery outcome recorded |
| A5-AT-008 | Mobile SDK phone path | Phone receives app message on supported Android/iOS versions |
| A5-AT-009 | Storage block | Maximum block and aggregate quota measured |
| A5-AT-010 | Storage full | Existing active route remains valid |
| A5-AT-011 | Haptics | Turn cue delivered once without UI failure |
| A5-AT-012 | Long route | Rendering/storage/RAM behavior within measured limits |
| A5-AT-013 | Battery | Trip-duration drain measured against baseline |
| A5-AT-014 | Multi-watch | Only selected device receives transfer |
| A5-AT-015 | App restart | Active route/session is restored or explicitly invalidated |

No test above is marked passed by this specification.

## 9. Official references

- Compatible devices: https://developer.garmin.com/connect-iq/compatible-devices
- MapView: https://developer.garmin.com/connect-iq/api-docs/Toybox/WatchUi/MapView.html
- Communications: https://developer.garmin.com/connect-iq/api-docs/Toybox/Communications.html
- Application Storage: https://developer.garmin.com/connect-iq/api-docs/Toybox/Application/Storage.html
- Position: https://developer.garmin.com/connect-iq/api-docs/Toybox/Position.html
- Connect IQ API documentation: https://developer.garmin.com/connect-iq/api-docs/
