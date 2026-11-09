# Titanium Remote 2.0

Phone-side remote for the Titanium TV app (`TitaniumTV_0.3.504.wgt`).

- Pairing: the TV shows a 4-digit code + QR. Scan it (or type the code) once; the pairing is saved on both sides.
- Remote: arrows / OK / Back / Home, touchpad, play-pause, seek, CH +/-, keyboard text, IPTV source sending.
- Runs as a foreground service, so it keeps working with the screen off.
- Alexandria font and Titanium logo, 3 s intro.

Build: push to GitHub (Actions builds `app-release.apk` + `app-debug.apk`) or open in Android Studio.
The repo contains a fixed signing key (`app/titanium.jks`) so new builds install over old ones.
Protocol: see `TV_INTEGRATION_PROTOCOL.md` (v1 endpoints unchanged; added `X-Titanium-Code` pairing and `key` events).
