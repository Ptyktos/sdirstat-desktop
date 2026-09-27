# sdirstat-desktop

A [Tauri](https://tauri.app) shell around [sdirstat](https://github.com/Ptyktos/sdirstat)'s
built-in GUI. It launches the `sdirstat` CLI as a **sidecar** (`sdirstat serve` on a free
`127.0.0.1` port) and opens a native window pointed at it — so the same web GUI (treemap /
sunburst / type-stats / Open·Reveal·Trash) runs unchanged, with no frontend rewrite.

This repo carries the Tauri dependency tree; the core `sdirstat` crate it wraps stays
zero-dependency (std-only) in its own repo. The core is reused as-is via the sidecar — this app
adds no requirements to it.

## Prerequisites

- Rust (stable) and the Tauri CLI: `cargo install tauri-cli --version "^2" --locked`
- **Linux** system libs: `libwebkit2gtk-4.1-dev libgtk-3-dev libayatana-appindicator3-dev librsvg2-dev`
- **Windows**: WebView2 (preinstalled on Windows 10/11). **macOS**: Xcode command-line tools.

## Build & run

The sidecar is the [sdirstat](https://github.com/Ptyktos/sdirstat) CLI binary, staged under
`binaries/` with the target-triple suffix Tauri expects.

```sh
# build (or download) the sdirstat CLI, then stage it as this host's sidecar
mkdir -p binaries
cp /path/to/sdirstat binaries/sdirstat-$(rustc -vV | sed -n 's/host: //p')

# run the app (dev), or build native installers
cargo tauri dev
cargo tauri build --bundles deb      # Linux .deb   (AppImage: add `appimage`, best-effort)
cargo tauri build                    # Windows .msi + NSIS .exe
cargo tauri build --target universal-apple-darwin --bundles dmg   # macOS universal .dmg
```

CI does the same on tag push, pulling the matching CLI binary from
[sdirstat's latest release](https://github.com/Ptyktos/sdirstat/releases) instead of building it —
see [`.github/workflows/release.yml`](.github/workflows/release.yml).

> **Tested status:** only the Linux path (`.deb`) has been built and run. The Windows
> (`.msi`/NSIS) and macOS (universal `.dmg`) bundles are **untested** — no Win/Mac host was
> available — and may need iteration on their first tagged CI run.

## Packaging notes

- The Linux `.deb` package is `sdirstat-desktop` and declares `Provides`/`Conflicts`/`Replaces:
  sdirstat`, so it cleanly supersedes the CLI-only `sdirstat` package (it ships the CLI too, at
  `/usr/bin/sdirstat`, alongside `/usr/bin/sdirstat-desktop`).
- The sidecar is terminated when the app exits (window close / quit) via `RunEvent::Exit`. A
  `SIGKILL` of the app is the one path Tauri can't intercept and could orphan the child.

## Release pipeline (TODO — not fully carried over from the monorepo split)

This repo was split out of `Ptyktos/sdirstat`'s `desktop/` directory, where CI used to build the
CLI and this app together in one job. Splitting them means this repo's own `release.yml` now
downloads a released CLI binary from `Ptyktos/sdirstat` instead of building it — see that
workflow for the mechanics. Still outstanding, and requiring an owner decision rather than a
mechanical fix:

- **Code signing**: Apple Developer / Windows Authenticode certs were never configured in the
  original repo either (see its `docs/SIGNING.md`) — unsigned builds ship as before.
- **winget / chocolatey**: the original `publish.yml` bundled this app's `.msi` into the winget
  submission and chocolatey package. That cross-repo reference needs to move here (or be
  redesigned around two independently-versioned releases) — not done in this split.
