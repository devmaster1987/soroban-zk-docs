---
sidebar_position: 2
---

# SDK API Reference

Complete API reference for `soroban-zk-sdk`.

---

## `SorobanZKClient`

```ts
new SorobanZKClient(config: ClientConfig): SorobanZKClient
```

### `ClientConfig`

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `network` | `"testnet" \| "mainnet" \| "futurenet"` | Yes | Stellar network |
| `verifierContract` | `string` | Yes | Groth16 verifier contract address |
| `rpcUrl` | `string` | No | Custom Soroban RPC endpoint |
| `keypair` | `Keypair` | No | Stellar keypair for signing transactions |

### Methods

#### `verifyProof(params)`

```ts
client.verifyProof(params: VerifyParams): Promise<VerifyResult>
```

| Param field | Type | Description |
|-------------|------|-------------|
| `circuit` | `string` | Circuit ID registered on-chain |
| `proof` | `Groth16Proof` | Proof object from a Prover |
| `publicInputs` | `Record<string, bigint>` | Public signal values |

Returns `{ success: boolean, txHash?: string, error?: string }`.

#### `registerCircuit(params)`

```ts
client.registerCircuit(params: RegisterParams): Promise<string>
```

Registers a verification key on-chain. Returns the transaction hash. Requires admin keypair.

---

## `IdentityProver`

```ts
new IdentityProver(options?: ProverOptions): IdentityProver
```

### `ProverOptions`

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `artifactsDir` | `string` | `"./artifacts"` | Directory containing `.wasm` and `.zkey` |
| `threads` | `number` | `4` | Worker threads for proof generation |

### `prove(inputs)`

```ts
prover.prove(inputs: IdentityInputs): Promise<ProofResult>
```

| Input field | Type | Description |
|-------------|------|-------------|
| `secret` | `bigint` | Private secret |
| `nullifierSeed` | `bigint` | Unique nullifier seed |

Returns `{ proof: Groth16Proof, publicSignals: bigint[] }`.

### `computeCommitment(secret)`

```ts
prover.computeCommitment(secret: bigint): bigint
```

Computes `Poseidon(secret)` synchronously. Useful for pre-computing commitments.

---

## `MerkleTree`

```ts
new MerkleTree(depth: number, hashFn?: HashFn): MerkleTree
```

Default hash function is Poseidon over BN254.

### Methods

| Method | Signature | Description |
|--------|-----------|-------------|
| `insert` | `(leaf: bigint) => void` | Insert a leaf |
| `proof` | `(leaf: bigint) => MerkleProof` | Generate inclusion proof |
| `verify` | `(leaf: bigint, proof: MerkleProof) => boolean` | Verify a proof locally |
| `root` | `bigint` (getter) | Current Merkle root |

### `MerkleProof`

```ts
interface MerkleProof {
  pathElements: bigint[];
  pathIndices: number[];  // 0 = left, 1 = right
  root: bigint;
}
```

---

## `poseidon`

```ts
import { poseidon } from "soroban-zk-sdk/crypto";

poseidon(inputs: bigint[]): bigint
```

Computes the Poseidon hash over BN254 field elements. Supports 1–16 inputs.
