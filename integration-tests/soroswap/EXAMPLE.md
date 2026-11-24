# Which deadlines does Soroswap accept?

The vault forwards its deadline to the Soroswap router. The native mock used to accept expired deadlines. These tests build the current vault and use [Kanatoko](https://crates.io/crates/kanatoko) to run add and withdraw against the router selected by the mainnet factory.

| Deadline passed by the vault | Router result |
| --- | --- |
| Ledger sequence + 60 | Rejected. Soroswap expects Unix seconds. |
| An expired timestamp or the current timestamp | Rejected. The checked balances and position stay as they were. |
| A future timestamp | Accepted. A withdrawal retried one second past the current timestamp closes the position. |

The native mock now enforces the same boundary. The two Protocol 30 snapshots are from mainnet ledgers 65646127 (add) and 65646129 (withdraw). Funding and authorization are local to the test.

[Read the Rust assertions](tests/deadline.rs#L111).

## Reproduce locally

Use Rust 1.95.0 with the `wasm32v1-none` target. From the repo root, build the contracts, fetch the locked test dependencies once, then run the test offline.

```sh
cargo +1.95.0 build --locked --manifest-path SmartContract/Cargo.toml --target wasm32v1-none --profile release -p lobster-contract --target-dir integration-tests/soroswap/target
cargo +1.95.0 fetch --locked --manifest-path integration-tests/soroswap/Cargo.toml
cargo +1.95.0 test --locked --offline --manifest-path integration-tests/soroswap/Cargo.toml
```
