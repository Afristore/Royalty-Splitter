# Royalty-Splitter


The royalty distribution contract for the [Afristore Marketplace](https://github.com/Afristore/marketplace) — extracted from the monorepo into its own dedicated repository.

This Soroban smart contract implements:
- `initialize` — Set up the contract with admin address and initial recipient splits
- `distribute_royalties` — Distribute incoming royalty tokens proportionally to all recipients
- `update_royalty_split` — Admin-only function to update the percentage allocations per recipient
- `get_split` — Query the current split configuration

**Invariants enforced by the contract:**
- All recipient shares must sum to exactly 100%
- Only the admin can call `update_royalty_split` (enforced via `require_auth`)
- Invalid splits (not summing to 100%) are rejected on initialization and update

---

##  Getting Started (once code is populated)

```bash
# Build the contract WASM
cargo build --target wasm32-unknown-unknown --release

# Run all tests
cargo test --features testutils

# Check formatting and linting
cargo fmt --check
cargo clippy -- -D warnings

# Optimize the WASM binary
stellar contract optimize --wasm target/wasm32-unknown-unknown/release/royalty_splitter.wasm

# Deploy to testnet
stellar contract deploy --wasm target/wasm32-unknown-unknown/release/royalty_splitter.optimized.wasm --network testnet
```

---

##  Prerequisites

- Rust (stable)
- `wasm32-unknown-unknown` target: `rustup target add wasm32-unknown-unknown`
- Stellar CLI: `cargo install --locked stellar-cli`

---

##  Security

- `update_royalty_split` requires admin authorization via `require_auth` — non-admin calls will panic.
- Any split configuration where shares do not sum to 100% is rejected.

---

##  Source Location (Monorepo)

This repository is being extracted from [`afristore/contracts/royalty-splitter/`](https://github.com/Afristore/marketplace/tree/master/contracts/royalty-splitter) in the main monorepo.

**Tracked in:** [ui-ux-issues.md — Issue 125](https://github.com/Afristore/marketplace/blob/master/ui-ux-issues.md)

---

##  Contributing

1. Fork this repository
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Make your changes and ensure all CI checks pass:
   - `cargo fmt --check` — must exit 0
   - `cargo clippy -- -D warnings` — zero warnings
   - `cargo test --features testutils` — full royalty lifecycle tests must pass (initialize → distribute → update split → distribute again)
   - Non-admin `update_royalty_split` calls must panic/fail with auth error
   - Invalid splits (not summing to 100%) must be rejected
   - `cargo build --target wasm32-unknown-unknown --release` — must produce a valid WASM
4. Open a PR — **all CI must pass before a PR is eligible for review and merge**

---

##  Tech Stack

- Rust + `soroban-sdk v25.3.0`
- Stellar / Soroban smart contract platform

---

## 📄 License

MIT — see [LICENSE](./LICENSE)
