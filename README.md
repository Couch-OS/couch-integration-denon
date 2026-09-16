# Couch Denon integration

This repository builds the independently installable Denon AVR integration for
Couch. It controls the receiver's main zone over Denon's CR-delimited TCP
protocol and exposes power, volume, mute, and input selection through Couch's
versioned subprocess protocol.

The native implementation also remains in the Couch monorepo while independent
package distribution is introduced. Keep the two implementations byte-for-byte
aligned until the monorepo copy is deliberately retired.

## Build and test

The project depends on `couch-plugin` and `couch-sdk` from one exact Couch Git
revision. It does not copy their protocol types or admission harness. The lock
file and Git revision are both part of review.

```sh
cargo test --locked
cargo build --locked --release --bin couch-plugin-denon
cargo build --locked --release --target armv7-unknown-linux-musleabihf --bin couch-plugin-denon
cargo fmt -- --check
```

The admission test launches the real plugin executable against the reusable
`couch-plugin::testing` harness. It covers offline configuration, declared
capabilities, malformed and disconnected receivers, timeouts without implicit
retry, bounded queue pressure, and concurrent startup.

To update the Couch SDK contract, change every `couch-plugin` and `couch-sdk`
`rev` in `Cargo.toml` to the same reviewed full commit, regenerate `Cargo.lock`,
and rerun the complete test suite. Never vendor or locally redefine the framed
protocol.

## Feed metadata

`integration.json` is the machine-readable source contract used by the curated
Couch integration feed. `plugin.json` is the runtime manifest installed beside
the executable. A release becomes publishable only after the feed pins this
repository at a full commit and its unprivileged admission workflow succeeds.
The protected feed job signs the admitted binary later; this repository has no
signing key and cannot publish an official package by itself.

## Receiver setup

Enable **Network Control** on the Denon receiver and give it a stable address.
The default control port is 23. Connection settings are private runtime data and
must not be committed.

Licensed under GPL-3.0-or-later. See [LICENSE](LICENSE).
