# Clippy

[English](README.md) | [Español](README.es.md)

An ultralight instant-replay recorder for Windows. It continuously records in the background
(in RAM), and pressing one key (F9) saves the last few seconds as an MP4. The entire video path
runs on the GPU (DXGI Desktop Duplication → D3D11 NV12 → Media Foundation H.264 hardware encoder).

The interface supports English, Spanish, and Portuguese. Automatic mode follows the Windows
display language, and a specific language can be selected under General → Language.

Downloads and news are available at [clpy.vercel.app](https://clpy.vercel.app). Published versions
are available from the [upstream releases](https://github.com/santiremedi/clippy/releases), which
are also used by the automatic updater.

## Building

```bash
cargo build --release
```

The executable is created at `target/release/clippy.exe`. For publishing, it is renamed to
`Clippy.exe` because the updater looks for that exact asset name in the latest release.

For development and the windowless test modes, use the fast profile. It skips LTO and usually
builds in seconds; the result is `target/fast/clippy.exe`:

```bash
cargo build --profile fast --features devtools
```

## Executable modes

| Arguments | Behavior |
| --- | --- |
| (none) / `--background` | Recorder with a system-tray icon (`--background` starts it without opening a window, for Windows startup). |
| `--settings` | Main window and settings. |
| `--editor [file]` | Clip editor. |
| `--apply-update <path>` | Used by the updater to replace the installed executable and reopen it. |

Only with `--features devtools`:

| Arguments | Behavior |
| --- | --- |
| `--test <seconds> <folder> [game\|desktop\|mic\|juegos\|720\|1080\|1440]` | Records, saves a clip, and exits. It prints the encoder and detected game; a number selects the clip resolution. |
| `--test-mic` | Measures how much audio each microphone provides in 3 seconds. |
| `--test-download` | Downloads the published version like the Update button. |
| `--test-export`, `--test-frame`, `--test-strip` | Editor tests; see `src/editor/devtools.rs`. |

## Code map

```
src/
  main.rs          selects the executable mode
  config.rs        config.json (recording, audio, global shortcuts, language)
  i18n.rs          interface languages (Spanish, English, Portuguese; auto follows Windows) and `tr!`
  log.rs           %APPDATA%\Clippy, clippy.log, and timestamps
  mf.rs            Media Foundation and the app-wide audio format (48 kHz stereo PCM, AAC)
  clipcache.rs     per-clip waveform and keyframe cache (written by the recorder, read by the editor)
  app/             Windows application process
    mode.rs        modes, arguments, single-instance locks, window titles, and tray messages
    tray.rs        system tray, global shortcuts, and recorder control
    update.rs      updates (at most one Releases request per day)
    autostart.rs   Windows startup (HKCU\...\Run)
    keys.rs        Win32 key names
  record/          recording
    recorder.rs    recording thread: capture -> encoder -> buffer -> save
    game.rs        game detection (full-screen window), used for video and audio
    capture.rs, convert.rs, encoder.rs, audio.rs, mux.rs, clock.rs
  settings/        main window (mod.rs: state; pages.rs: pages)
  editor/          clip editor (separate process)
    mod.rs         window and main loop
    timeline.rs    tracks and pieces, linked video/audio, snapping, fades, and undo
    playback.rs    playback, visible/audible piece players, layers, and fades
    export.rs      export dialog and job
    panels.rs      top bar and timeline (tracks, markers, clip context menu)
    browser.rs     library panel: selection, folders, moving, and Recycle Bin
    inspector.rs   clip panel: image, speed, and sound
    preview.rs     preview canvas, aspect ratio, and image crop
    prefs.rs       editor.json (shortcuts, latest export options, panels, snapping)
    media.rs       export: stream copy or GPU-positioned/blended layers, speed, and audio mixing
    shortcuts.rs, library.rs, player.rs, devtools.rs
  ui_kit/          styling, widgets, native windows, icons, and sounds
    watch.rs       wakes a window when external state changes, without repainting while idle
```

## User files

Everything is stored under `%APPDATA%\Clippy`: `config.json`, `editor.json`, `library.json`,
`update.json`, `clippy.log`, and `cache\`.

## Publishing a version

1. Increase the version in `Cargo.toml`.
2. Open GitHub → Actions → **Build** → *Run workflow*, enter a tag such as `v0.3.0`, and select
   “Publish release.” The workflow builds and uploads `Clippy.exe`; if the release does not exist,
   it creates a draft. Without that option, the executable is still available as a workflow artifact.
3. Write user-facing release notes before publishing. Clippy displays headings (`#`), lists (`-`),
   and paragraphs, without bold markers or backticks. A published release without text does not show news.

## License

[GPL-3.0-or-later](LICENSE). You may use, study, modify, and redistribute Clippy. Distributed modified
versions must include their source code under the same license. The “Clippy” name and icon may not be
used to identify modified versions.
