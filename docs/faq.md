---
sidebar_position: 10
---

# FAQ

Frequently asked questions about the Soroban ZK Toolkit.

## General

### Do I need to understand cryptography to use the toolkit?

No. The SDK abstracts all cryptographic details. You work with high-level concepts like commitments, proofs, and nullifiers. Understanding [basic ZK concepts](./concepts/zero-knowledge.md) helps, but it is not required to ship an integration.

### Is the trusted setup safe?

The toolkit ships pre-generated trusted setup artifacts for all four circuits. The setup was run as a multi-party computation (MPC) ceremony — as long as at least one participant was honest, the setup is secure. Ceremony transcripts are published in the repository for public audit.

If your threat model requires a custom ceremony, you can run your own using `snarkjs` with the provided `.r1cs` files.

### Can I use the toolkit on Stellar Mainnet?

Yes. The contracts are deployed on both Testnet and Mainnet. Switch the `network` parameter in `SorobanZKClient` to `"mainnet"`. Ensure you have sufficient XLM for contract invocation fees.

## Technical

### Why BN254 and not BLS12-381?

BN254 (also called alt\_bn128) has native precompile support in the EVM, making it the most widely tested pairing-friendly curve. Soroban's host environment supports BN254 arithmetic at the metered instruction level.

### How do I prevent proof replay attacks?

Use **nullifiers**. Every circuit in the toolkit outputs a nullifier: `Poseidon(secret, nullifierSeed)`. Your contract should:

1. Accept the nullifier as a public input.
2. Check it has not been stored before.
3. Store it after a successful verification.

### How big are the proofs?

Groth16 proofs are always **256 bytes** regardless of circuit complexity. This is one of the key advantages of Groth16 for on-chain use.

### How long does proof generation take?

| Circuit | Node.js (M2) | Browser (WASM) |
|---------|-------------|----------------|
| identity | ~0.3s | ~1.0s |
| membership (depth-20) | ~1.2s | ~4.0s |
| range-proof | ~0.5s | ~1.8s |
| transfer | ~0.9s | ~3.0s |

### Can I run proof generation in the browser?

Yes. All circuits are compiled to WebAssembly. Import from `soroban-zk-sdk/browser` for tree-shaken browser builds that exclude Node.js-specific modules.

## Troubleshooting

### `Artifacts not found`

Run `npx soroban-zk-sdk download-artifacts --circuit <name>` to download the `.wasm` and `.zkey` files.

### `Invalid proof` returned by contract

Check that the `publicInputs` passed to `verifyProof` exactly match the `publicSignals` output from the prover in the same order.

### `CircuitNotFound` error from contract

The verifier contract does not have a registration for that `circuit_id`. Run `register_circuit` with admin credentials.

### Proof generation hangs

The `.zkey` file may be corrupted during download. Delete `artifacts/<circuit>.zkey` and re-run `download-artifacts`.
