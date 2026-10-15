---
description: "Follow user-visible changes to Nexo documentation and SDK integration."
---

# Changelog

Follow changes that affect how you build and run your bot. The Atom feed links to these entries. API versions and SDK package versions are separate. Read [Versioning & deprecation](versioning.md) before upgrading a dependency.

## October 15, 2026 <a href="#october-15-2026" id="october-15-2026"></a>

### Developer guides <a href="#developer-guides" id="developer-guides"></a>

You can follow the quickstart in TypeScript, Python or Rust, then use the local emulator with any of the three clients. The guides explain token reveal, request envelopes, idempotency and choosing an update transport.

Use [SDKs and CLI](sdks.md) to install the package for your language:

- TypeScript uses `@arx/nexo`.
- Python uses the distribution `arx-nexo`, imported as `arx_nexo`.
- Rust uses the crate `arx-nexo`.

### Standalone CLI <a href="#standalone-cli" id="standalone-cli"></a>

The `arx-nexo` command is a Rust application in the repository's `CLI/` directory. Install it with `cargo install --path CLI`, or run it with `cargo run --release --manifest-path CLI/Cargo.toml -- whoami`. It includes YAML commands and profiles, webhook tools, signature verification and the local emulator. Python is no longer required to run the CLI or emulator.

Use `arx-nexo new DIR --lang typescript`, `--lang python` or `--lang rust` to create an echo project configured for the emulator. `arx-nexo emulator-token` prints its synthetic token. Python tests can keep using `Emulator(port=0)` after installing the CLI; the helper finds the executable on `PATH` or through `ARX_NEXO_CLI`.

The Python package no longer provides the `cli` extra, the `arx-nexo` console entry point or `python -m arx_nexo`. Install the [standalone CLI](sdks.md#cli) to use those tools.

The [capability map](features.md) points to supported features, and [Not available yet](not-available-yet.md) collects features outside the current Bot API. The [API reference](api.md) groups operations and links to their types.

## What appears here <a href="#what-appears-here" id="what-appears-here"></a>

Entries describe changes to public behavior, request or response fields, SDK integration and developer guidance. A breaking change or deprecation entry identifies the affected feature and the action you need to take.

Continue with [Versioning & deprecation](versioning.md) to plan an upgrade, or return to the [quickstart](quickstart.md) to build a bot.
