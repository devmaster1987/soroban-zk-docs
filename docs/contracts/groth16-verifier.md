---
sidebar_position: 2
---

# Groth16 Verifier Contract

The `groth16-verifier` contract is the core of the on-chain ZK verification layer. It exposes two entry points: registering a circuit's verification key, and verifying a proof.

## Interface

### `register_circuit`

Registers the verification key for a circuit. Admin-only.

```rust
pub fn register_circuit(
    env: Env,
    circuit_id: Symbol,
    vk: VerificationKey,
) -> Result<(), Error>
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `circuit_id` | `Symbol` | Short identifier, e.g. `"identity"` |
| `vk` | `VerificationKey` | Groth16 verification key struct |

### `verify_proof`

Verifies a Groth16 proof for the given circuit and public inputs.

```rust
pub fn verify_proof(
    env: Env,
    circuit_id: Symbol,
    proof: Proof,
    public_inputs: Vec<U256>,
) -> Result<bool, Error>
```

| Parameter | Type | Description |
|-----------|------|-------------|
| `circuit_id` | `Symbol` | Must match a registered circuit |
| `proof` | `Proof` | Groth16 proof `(pi_a, pi_b, pi_c)` |
| `public_inputs` | `Vec<U256>` | Public input scalars in circuit order |

**Returns** `true` if the proof is valid, `false` otherwise. Panics on malformed inputs.

## Data types

### `VerificationKey`

```rust
pub struct VerificationKey {
    pub alpha_g1: G1Point,
    pub beta_g2: G2Point,
    pub gamma_g2: G2Point,
    pub delta_g2: G2Point,
    pub ic: Vec<G1Point>,   // One point per public input + 1
}
```

### `Proof`

```rust
pub struct Proof {
    pub pi_a: G1Point,
    pub pi_b: G2Point,
    pub pi_c: G1Point,
}
```

### `G1Point` / `G2Point`

BN254 elliptic curve points encoded as big-endian byte arrays.

```rust
pub struct G1Point { pub x: Bytes, pub y: Bytes }
pub struct G2Point { pub x: [Bytes; 2], pub y: [Bytes; 2] }
```

## Error codes

| Code | Name | Description |
|------|------|-------------|
| 1 | `CircuitNotFound` | No verification key registered for the given `circuit_id` |
| 2 | `InvalidProofFormat` | Proof points are not on the BN254 curve |
| 3 | `PublicInputMismatch` | Number of public inputs does not match the verification key |
| 4 | `Unauthorized` | Caller is not the contract admin |

## Example invocation (TypeScript SDK)

```ts
import { SorobanZKClient } from "soroban-zk-sdk";

const client = new SorobanZKClient({
  network: "testnet",
  verifierContract: "CXXX...",
});

const verified = await client.verifyProof({
  circuit: "identity",
  proof,
  publicInputs,
});
```

## Gas cost

Groth16 verification on Soroban costs approximately **500,000–700,000 instructions** depending on the number of public inputs (which affects the multi-scalar multiplication in the verification equation).
