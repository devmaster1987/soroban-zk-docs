---
sidebar_position: 1
---

# Contracts Overview

The Soroban ZK Toolkit ships one primary smart contract: the **Groth16 verifier**. Additional helper contracts are provided for common patterns.

## Contract list

| Contract | Description | WASM size |
|----------|-------------|-----------|
| `groth16-verifier` | Verifies Groth16 proofs for any registered circuit | ~85 KB |
| `identity-registry` | Example contract using the verifier for identity claims | ~40 KB |
| `merkle-root-store` | Stores and updates Merkle roots on-chain | ~25 KB |

## Deployment

Contracts are pre-compiled and available in the `contracts/` directory of the toolkit repository.

### Deploy to Testnet

```bash
soroban contract deploy \
  --wasm contracts/groth16-verifier.wasm \
  --network testnet \
  --source <your-keypair>
```

### Initialize the verifier

After deployment, register the verification key for each circuit you plan to use:

```bash
soroban contract invoke \
  --id <contract-id> \
  --network testnet \
  --source <your-keypair> \
  -- register_circuit \
  --circuit_id identity \
  --vk_json "$(cat artifacts/identity.vk.json)"
```

## Security model

- The verifier contract is **stateless** — it does not store proofs or nullifiers. Replay prevention is the responsibility of the calling application.
- Verification keys are stored in contract storage and can only be updated by the contract admin.
- The verifier uses BN254 curve arithmetic via Soroban host functions.

## See also

- [Groth16 verifier reference](./groth16-verifier.md)
