# TitaniumTV 0.3.503 — Titanium Remote protocol v1

This Android project implements the phone-side HTTP server expected by `TitaniumTVbeta3_503.wgt`.

## Pairing
1. Put the Android phone and Samsung TV on the same Wi-Fi/LAN.
2. Open Titanium Remote and keep it in the foreground.
3. On the TV open Settings → Phone remote.
4. Enter the phone's displayed IPv4 address (port 8765 is the default) and pairing code.
5. The TV performs `GET /health`, then `GET /pair` with `X-Titanium-Token: <pairing code>`.

## Polling
After pairing, the TV polls `GET /next` with the same `X-Titanium-Token` header. The phone returns one queued JSON event or `{"type":"none"}`.

### Text event
```json
{"type":"text","text":"typed text"}
```

### Xtream Codes event
```json
{"type":"iptv_credentials","format":"xtream","server":"http://host:port","username":"user","password":"pass"}
```

### M3U event
```json
{"type":"iptv_credentials","format":"m3u","url":"https://example.test/list.m3u"}
```

The WGT fills its existing Add Source form. The user must review and confirm on the TV; this app does not save a subscription by itself.

## HTTP behavior
- Port: TCP 8765, bound to all IPv4 interfaces.
- Routes: `GET /health`, `GET /pair`, `GET /next`, `GET /ack`.
- `OPTIONS` is answered with CORS headers, including `Access-Control-Allow-Headers: X-Titanium-Token, Content-Type`, to support browser/XHR preflight if the TV runtime requests it.
- Responses are JSON, no-store, and connection-close.
- Protocol version: `1`; health response app name must be exactly `Titanium Remote`.

## Security and limitations
This is a local-network prototype. HTTP and the pairing token are not encrypted; use only on a trusted private Wi-Fi network, never port-forward 8765 to the internet. Keep the app open while connected. Android may stop the server if the app is backgrounded or the OS kills it. The WGT itself only acts on `text` and `iptv_credentials` events.

## 0.3.504 / app 2.0 additions
- `GET /health` also returns `id`, `name`, `pairable`.
- First pairing: TV shows a 4-digit code; the phone user enters/scans it; the TV scans its /24 for `/health`, then `GET /pair` with `X-Titanium-Code: <code>`. A matching code returns `{"paired":true,"token":"<32 hex>"}`. The TV stores token + phone id and later authenticates with `X-Titanium-Token`. Five different wrong codes lock pairing for 60 s.
- `/next` is a long poll (≤ 2.5 s, `"lp":true` on empty answers).
- New event: `{"type":"key","key":"up|down|left|right|enter|back|home|play_pause|ff|rw|ch_up|ch_down|info","action":"press|down|up","rep":false}`.
