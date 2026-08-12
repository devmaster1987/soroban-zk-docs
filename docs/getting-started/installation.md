---
sidebar_position: 1
---

# Installation

This page covers installing all components of the Soroban ZK Toolkit.

## Prerequisites

| Dependency | Version | Purpose |
|-----------|---------|---------|
| Node.js | ≥ 18 | SDK and tooling |
| Rust | stable | Soroban contract compilation |
| Soroban CLI | ≥ 0.9 | Contract deployment |
| Circom | 2.x | Circuit compilation (optional) |

### Install Node.js

```bash
# Using nvm (recommended)
nvm install 20
nvm use 20
```

### Install Rust and Soroban CLI

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup target add wasm32-unknown-unknown
cargo install --locked soroban-cli
```

### Install Circom (optional — only needed for circuit development)

```bash
curl -sSf https://circom.dev/install.sh | sh
```

## Install the SDK

```bash
npm install soroban-zk-sdk
```

Or with pnpm / yarn:

```bash
pnpm add soroban-zk-sdk
yarn add soroban-zk-sdk
```

## Verify installation

```ts
import { SorobanZKClient } from "soroban-zk-sdk";

const client = new SorobanZKClient({ network: "testnet" });
console.log("SDK version:", client.version);
```

## Download circuit artifacts

The toolkit ships pre-compiled `.wasm` and `.zkey` files. Download them for the circuits you need:

```bash
npx soroban-zk-sdk download-artifacts --circuit identity
npx soroban-zk-sdk download-artifacts --circuit membership
npx soroban-zk-sdk download-artifacts --circuit range-proof
npx soroban-zk-sdk download-artifacts --circuit transfer
```

## Next steps

- [Quickstart](./quickstart.md) — generate your first proof in 5 minutes
- [First proof tutorial](./first-proof.md) — end-to-end walkthrough
