# TV Remote · multi-driver

Minimal, single-file web remote for LAN-controllable Smart TVs. No build step,
no dependencies — open `index.html` in a browser on the same Wi-Fi as the TV.

A driver layer maps one logical button set to each brand's protocol. What a
plain local HTML file can reliably reach (plain HTTP, no pairing/cert):

| Brand | Protocol / port | Support |
|---|---|---|
| **Philips** | JointSpace `:1925` | ✅ full — status, volume slider, all keys |
| **Roku / TCL-Roku** | ECP `:8060` | ✅ commands (fire-and-forget; responses unreadable in-browser due to no CORS) |
| **Samsung / LG / Sony** | WebSocket/REST w/ token·pairing·TLS | ⛔ not from a local file — the browser can't negotiate their auth; use the official app |

**Discover** auto-detects Philips (confirmed by reading `/system`) or Roku
(port `:8060` open) on your subnet and selects the right driver.

![status: single-file](https://img.shields.io/badge/single--file-HTML-38e1b0) ![deps: none](https://img.shields.io/badge/deps-none-2bb3ff)

## Features

- **Live status** (polled every 4 s): model, current activity, volume, Ambilight mode, connection indicator.
- **Volume** slider + mute, synced to the TV's real volume.
- **D-pad** navigation (▲▼◀▶ / OK), Back, Home, Options.
- **Playback**: rewind / play-pause / stop / fast-forward.
- **Channel / Source / Standby** (Standby asks for confirmation).
- **Numeric keypad** 0–9.
- **Discover TV**: scans the local `/24` for a responding JointSpace device and fills in its IP.
- **Console** showing each command sent and any network error.

## Usage

1. Open `index.html` in your browser (double-click, or `open index.html`).
2. It defaults to `192.168.18.10`. If your TV is elsewhere, click **Descubrir TV** or type the IP.
3. Control away.

Must be run as a local file (`file://`). It talks to the TV over plain HTTP on
your LAN, which an HTTPS-hosted page cannot do (mixed-content). Commands are
sent as "simple" `POST text/plain` requests to avoid a CORS preflight the TV
rejects; reads are plain `GET`s. The API already returns
`Access-Control-Allow-Origin: *`.

## API reference

Built against JointSpace API v6 (`http://<tv-ip>:1925/6/...`):

| Action | Request |
|---|---|
| Model / system info | `GET /6/system` |
| Volume read | `GET /6/audio/volume` |
| Volume set | `POST /6/audio/volume` `{"muted":false,"current":N}` |
| Remote key | `POST /6/input/key` `{"key":"Standby"}` |
| Current context | `GET /6/context` |
| Ambilight mode | `GET /6/ambilight/mode` |

Common keys: `Standby`, `VolumeUp`, `VolumeDown`, `Mute`, `CursorUp/Down/Left/Right`,
`Confirm`, `Back`, `Home`, `Options`, `ChannelStepUp/Down`, `Source`, `WatchTV`,
`PlayPause`, `Stop`, `FastForward`, `Rewind`, `Digit0`–`Digit9`.

## Notes

The default IP is a non-routable private address (RFC 1918) and exposes nothing.
No credentials or device identifiers are stored in this project.

## License

MIT
