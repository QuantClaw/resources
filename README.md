# QuantClaw Resources

Pre-built vcpkg dependency packages for [QuantClaw](https://github.com/QuantClaw/QuantClaw) CI pipelines.

## Purpose

Eliminate cold-compile time in CI by providing pre-built vcpkg packages as GitHub Release assets.
Instead of compiling spdlog / curl / OpenSSL / ixwebsocket from source on every cache miss,
CI downloads a tarball from here and extracts it directly.

## Release naming

| Platform | Tag pattern | Assets |
|----------|-------------|--------|
| Windows x64 | `vcpkg-windows-x64-<baseline8>` | `vcpkg-bincache-windows-x64.tar.gz`, `vcpkg-installed-windows-x64.tar.gz` |
| Linux x64   | `vcpkg-linux-x64-<baseline8>`   | `vcpkg-bincache-linux-x64.tar.gz`,   `vcpkg-installed-linux-x64.tar.gz`   |

`<baseline8>` = first 8 chars of the vcpkg `builtin-baseline` hash in `vcpkg.json`.

## Updating packages

Push a change to `vcpkg.json` (update `builtin-baseline` or add/remove packages) and the
`build-vcpkg` workflow will automatically rebuild and publish a new release.

You can also trigger a manual rebuild via **Actions → Build vcpkg packages → Run workflow**.

## How QuantClaw CI uses these

1. Look up the release tag matching the current `builtin-baseline`.
2. Download and extract the binary cache tarball to the local vcpkg binary cache directory.
3. Run `cmake` with `VCPKG_BINARY_SOURCES` pointing at that directory — vcpkg finds all packages
   as cache hits and skips recompilation entirely.
4. Fall back to normal vcpkg install if the release is not yet available.
