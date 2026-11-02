# LE Media Player

A fast, native desktop media player for Linux with a real-time equalizer, loudness control and a large set of music visualizers — built in Rust with no browser engine inside. It plays local audio and video, YouTube and other URLs, and keeps a Winamp-style playlist under keyboard and drag-and-drop control, for people who want one lightweight player for listening, tuning sound and watching video.

## Tech stack

| Layer | Technologies |
|-------|--------------|
| Language | Rust (2021 edition) |
| Audio | cpal output, ffmpeg decoding, rustfft analyser, lock-free ring buffers |
| DSP | Custom 20-band biquad equalizer, loudness normalizer, soft limiter, WSOLA time stretch |
| UI | egui / eframe (OpenGL), Slint, GTK4 — three frontends on one engine library |
| Integration | MPRIS over D-Bus (zbus), X11 and Wayland, `.desktop` entry, portable bundle |
| Online sources | yt-dlp |

## Origin: a Rust rewrite of the AFX lab

LE Media Player is the Rust rewrite of the AFX lab from the Logic Encoder karaoke studio — the browser-based audio-effects and visualizer workspace — as a standalone desktop application with no browser engine. The audio engine, the signal chain and the visualizers were reimplemented in Rust and run in the same process: ffmpeg decodes, a native output stream plays, and the 20-band equalizer, loudness normalizer, soft limiter and spectrum analyser use the same laws as the web version, so a preset sounds the same in both. The lab started as a port and grew into a full player while keeping effects and visualizers as its signature.

## Beyond the browser lab

Moving the engine into a native process opened up features that a web page cannot offer, and the player adds them on top of the original lab:

- **Pitch-preserving tempo** with gapless rate changes, using a WSOLA time stretcher inside the audio callback.
- **A-B loop, bookmarks, resume position and go-to-time** for studying or practising a passage.
- **CUE sheet support** that splits a single audio image into tracks.
- **Audio output device selection** and **media keys with now-playing info** through the desktop's media-player interface.
- **Multi-track audio and subtitle switching**, embedded subtitle extraction and subtitle delay.
- **Instant response on large libraries:** the playlist draws only visible rows, media probing runs in the background with a cache, Stop silences the stream immediately, and every potentially blocking call (probing, folder scans, URL resolving) is bounded by a watchdog so the interface never freezes.
- **Clear feedback for online sources:** a failed URL resolve reports the reason instead of waiting indefinitely.

## Sound and effects

The effects rack is built for tuning sound while it plays. A 20-band equalizer ships with presets such as Flat, Rock, Metal, Techno, Bass boost, Pop, Vocal and Classical, plus manual gain per band and a pre-amp. Loudness normalization evens out tracks recorded at different levels, and a soft limiter prevents clipping when bands are boosted. Playback rate can be changed without shifting pitch, and tracks switch quickly with instant Stop and clean Play because the DSP state persists between tracks. Every slider has stepper buttons for precise adjustment.

## Visualizers

Visualizers react to the live spectrum and to an adaptive beat detector. The collection covers bars, waveforms, particle effects and circular spectrum modes with a mirrored variant, a bass-driven orb, frequency rings and a cymatics plate. A reactivity slider controls how strongly bass drives motion, a mono/left-right toggle selects the channel source, and colour accents follow the selected scheme. The effects pane can be toggled away for a clean video or playlist view.

## Playlist

The playlist follows Winamp conventions. Files and whole folders can be dropped in; folders are scanned recursively and the drop position decides where items land, with an insertion line and auto-scroll while dragging. Selection supports multi-select and block moves, and a context menu offers sort and remove operations. M3U, M3U8, PLS and CUE playlists load and M3U, M3U8 and PLS playlists save. A duration column, now-playing chip, shuffle and loop modes that follow Winamp semantics, and a keyboard map keep it fast to drive. Long lists stay smooth because only visible rows are drawn.

## Video and subtitles

Video plays in a resizable panel with MPC-HC-style context menu and single-row or two-row transport. Player essentials include an on-screen display, A-B loop, frame stepping, configurable seek steps, go-to-time, aspect ratio, brightness and contrast adjustments, rotation, deinterlacing and snapshots. Subtitles load from SRT and VTT files or are extracted from embedded tracks, with adjustable delay, and multi-track audio and subtitle switching is available. The player resumes where playback stopped and keeps bookmarks and a recent-files list.

## Online sources

Any URL that yt-dlp understands can be opened directly. Video and audio streams are fetched separately and joined for playback, playlists expand into the queue, and a failed resolve reports the reason instead of hanging on a loading state.

## Desktop integration

Media keys and now-playing information work through MPRIS, so desktop widgets and keyboard shortcuts control playback. A file passed on the command line starts playing immediately, a desktop entry registers the player as a file handler, and a portable bundle can be built to run from any folder. On systems where the display server restarts, the player waits for a live display socket and reconnects, choosing between Wayland and X11 automatically.

## Frontends

The same engine library backs three interfaces: the full-featured egui application, a Slint frontend with a declarative layout, and a GTK4 frontend with native widgets and symbolic icons. All three decode, process and play audio identically.

Private code: [le-media-player](https://github.com/logicencoder/le-media-player)

---

Logic Encoder · [GitHub](https://github.com/logicencoder) · [logicencoder.com](https://logicencoder.com)
