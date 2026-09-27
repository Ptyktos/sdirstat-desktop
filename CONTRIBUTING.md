# Contributing

Thanks for considering a contribution. Issues and pull requests are welcome for bug fixes,
packaging improvements, and documentation.

## Before opening a pull request

- Search existing issues and pull requests; describe the user-visible problem and expected
  behavior.
- Keep changes focused. This repo has no automated test suite (a Tauri shell has little to unit
  test beyond the sidecar-lifecycle code in `src/main.rs`) — describe how you exercised a change
  manually (which OS, which bundle).
- Never commit a signing certificate, API token, or other secret alongside a packaging change.

## Development setup

```sh
cargo build --locked
cargo fmt --check
cargo clippy --all-targets --locked -- -D warnings
```

Building and running the app itself needs the sidecar staged first — see the main
[README](README.md#build--run).

## Pull requests

Open a pull request against `main`. Include a concise summary, which platform(s) you tested on,
and any packaging/signing impact. CI must pass before merge.

## Security reports

Do not report vulnerabilities in public issues or pull requests. Follow [SECURITY.md](SECURITY.md)
for private reporting.

## License

By contributing, you agree that your contribution is offered under this repository's dual
MIT/Apache-2.0 license.
