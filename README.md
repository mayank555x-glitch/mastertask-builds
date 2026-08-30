# MasterTask — ffmpeg builds

LGPL-only ffmpeg binaries used by MasterTask, built from FFmpeg's own source by the workflow in this
repository, and published as release assets.

## Why this repository is public

**Two reasons, and neither is that anyone is expected to browse it.**

1. **LGPL-2.1-or-later source availability.** MasterTask distributes these binaries publicly. The LGPL asks
   that the corresponding source and *"the scripts used to control compilation and installation"* be
   available. FFmpeg's source is upstream and public; the build recipe is
   [`.github/workflows/ffmpeg.yml`](.github/workflows/ffmpeg.yml), here.
2. **No credential in the publish path.** MasterTask's catalog builder fetches every artifact from a public
   upstream — llama.cpp, whisper.cpp, Hugging Face, Chrome for Testing. ffmpeg was the single exception, and
   that one exception is why a GitHub token had to exist at all. A token expires; a public release does not.

## What is built

`--disable-gpl --disable-nonfree`, static, self-contained. That keeps the build to the LGPL component set —
the licence family written for use inside closed commercial software — and, by construction, drops the
patent-encumbered encoders (x264/x265): **this build cannot encode H.264/H.265.** Decoding through the
native decoders remains, which is what video *ingest* needs.

| Platform | Built | Note |
|---|---|---|
| macOS arm64 · x64 | ✅ | x64 is cross-built on Apple Silicon — Intel runners queue for hours |
| Linux x64 · arm64 | ✅ | |
| Windows x64 | ✅ | cross-compiled from Linux with mingw-w64, as BtbN does |
| **Windows arm64** | ❌ | Ubuntu's mingw-w64 ships x86_64 and i686 only, so `aarch64-w64-mingw32` does not exist and the build fails at configure. It needs llvm-mingw — a separate toolchain with its own pinning |

## How a build is run

Actions → **ffmpeg (all platforms, LGPL)** → Run workflow → enter the FFmpeg tag (e.g. `n7.1.1`).
It creates a release tagged `ffmpeg-<version>-lgpl` carrying one asset per platform. GitHub publishes a
sha256 for every asset, so MasterTask verifies these on exactly the same path as every other artifact —
there is no "no digest" exception anywhere in the catalog.
