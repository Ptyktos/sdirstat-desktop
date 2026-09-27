# Security policy

## Report a vulnerability

Email **security@ptyktos.com** to report a suspected vulnerability. Do not open a public issue for
an unpatched vulnerability. Include the affected version or commit, impact, and steps to
reproduce.

Once GitHub private vulnerability reporting is enabled in repository settings, reports can also be
submitted through
[GitHub Security Advisories](https://github.com/Ptyktos/sdirstat-desktop/security/advisories/new).

## Supported versions

Before the first stable release, security fixes are made against the latest `main` branch.

## Security-relevant behavior

This app launches the [sdirstat](https://github.com/Ptyktos/sdirstat) CLI as a sidecar process
running `sdirstat serve`, which binds `127.0.0.1` exclusively (never a network interface) — see
that repo's own [SECURITY.md](https://github.com/Ptyktos/sdirstat/blob/main/SECURITY.md) for the
served endpoints' behavior (path guards on the action endpoint, trash-not-delete semantics). This
app does not change that surface; it only points a native window at it.

Tauri's own security model applies on top: `capabilities/default.json` scopes what the webview is
allowed to do (the `shell` plugin permission is limited to invoking the bundled sidecar, not
arbitrary commands). Review that file before widening it.

**Supply chain**: this app bundles a *compiled binary* of the `sdirstat` CLI at release time (a
Tauri "sidecar"), sourced from `Ptyktos/sdirstat`'s own GitHub Releases rather than built from
source in the same job. Verify the sidecar's checksum/signature against that repo's release
artifacts (see its own `RELEASE.md`/`docs/SIGNING.md`) before trusting a build of this app.

Release builds of this app are currently **unsigned** on Windows and macOS (no code-signing certs
configured yet) — expect Gatekeeper/SmartScreen warnings until that's set up.

## Dependency and release security

Rust dependencies are recorded in `Cargo.lock`; CI builds against it. Pull requests and scheduled
workflows run RustSec advisory checks, cargo-deny policy checks, SBOM generation, and OpenSSF
Scorecard.
