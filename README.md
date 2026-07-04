# Playlist Skimmer

A lightweight, single-file web app for quickly skimming through SoundCloud playlists. It plays every track in a playlist for a configurable number of seconds, moves through a queue of playlists automatically, and repeats the whole cycle for a configurable number of loops.

**Live app:** https://andreilucca.github.io/playlistskimmer/

## Features

- **Playlist queue** — add up to 5 SoundCloud playlists; they play back-to-back, in order.
- **Per-track skim interval** — each track plays for N seconds (2–60, default 5), then the app skips to the next one automatically.
- **Queue loops** — one loop = a full pass through every playlist in the queue. Set how many loops to run (e.g. 10); the app stops automatically when done. `0` = repeat forever.
- **Automatic transitions** — when a playlist finishes its pass, the next one loads and starts on its own. No interaction needed.
- **Mute toggle** — skim silently; playback logic is unaffected.
- **Live stats** — current playlist / track / loop counters, progress bar per track, and an activity log.
- **Session-scoped state** — the unlock and the queue live only for the current browser tab. Closing the tab or opening the app elsewhere starts fresh.
- **Access gate** — the app is protected by a passphrase prompt. The passphrase is not stored in the source; only its SHA-256 hash is embedded and compared client-side. Note: this is a convenience gate on a static page, not real access control.

## Usage

1. Open the app and unlock it.
2. Paste a SoundCloud playlist URL (`https://soundcloud.com/<user>/sets/<playlist>`) and press **Add**. Repeat for up to 5 playlists.
3. Set **sec / track** and **loops**.
4. Press **Start**. The app skims every track, hops across playlists, counts loops, and stops by itself at the configured limit. **Stop** pauses at any point.

Click a playlist in the queue to jump to it; the **×** removes it.

## How it works

Plain HTML/CSS/JS in one `index.html` — no build step, no dependencies beyond the official [SoundCloud Widget API](https://developers.soundcloud.com/docs/api/html5-widget). Playback runs through the standard SoundCloud embedded player. For each playlist transition the app builds a fresh player iframe (with `auto_play` set in the embed URL) instead of reusing the old one, which makes transitions deterministic. A generation guard discards events from torn-down players and a latch prevents double skips when the skim timer and a track's natural end race each other.

Because playback happens in the official embed, standard SoundCloud behavior applies — short automated plays are not a way to influence play counts, and the tool is not intended for that.

## Hosting

Served as a static site from this repository via GitHub Pages. To deploy your own: fork or copy `index.html`, enable Pages (Settings → Pages → Deploy from a branch → `main` / root), and replace the passphrase hash in the source with your own (`sha256` of your chosen passphrase, hex-encoded).

## Limitations

- Private playlists work only via their secret-link URL.
- The page must stay open in a foreground tab; browsers may throttle timers in background tabs.
- HTTPS is required (GitHub Pages provides it) — the unlock check uses the Web Crypto API.
