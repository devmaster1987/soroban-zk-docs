---
sidebar_position: 2
---

# Groth16

**Groth16** (Jens Groth, 2016) is the proving system used throughout the Soroban ZK Toolkit. It produces the smallest proofs of any pairing-based SNARK — just three elliptic-curve group elements — making it cost-effective to verify on-chain.

## Proof structure

A Groth16 proof consists of:

```
π = (A, B, C)
```

- `A` — a point on the G1 curve (BN254)
- `B` — a point on the G2 curve (BN254)
- `C` — a point on the G1 curve (BN254)

The verification key `vk` contains curve points derived from the circuit during the trusted setup.

## Verification equation

Verification checks a pairing equation:

```
e(A, B) = e(α, β) · e(∑ aᵢ · γᵢ, γ) · e(C, δ)
```

where `α, β, γ, δ` are elements of the verification key and `aᵢ` are the public inputs. The Soroban verifier contract implements this check using BN254 precompiles.

## Trusted setup

Groth16 requires a **circuit-specific trusted setup** — a multi-party computation (MPC) ceremony that produces the proving key and verification key. The setup must be re-run if the circuit changes.

The toolkit ships pre-generated keys for all four circuits. See the [trusted setup walkthrough](../getting-started/first-proof.md) if you need to run your own.

## Trade-offs vs other proving systems

| System | Proof size | Verify time | Setup |
|--------|-----------|-------------|-------|
| Groth16 | ~200 bytes | Fast | Circuit-specific |
| PLONK | ~400 bytes | Fast | Universal |
| STARKs | ~50–100 KB | Slow | None |

For on-chain verification on Soroban, Groth16's small proof size and fast verification outweigh its setup complexity.

## Resources

- [Groth16 paper](https://eprint.iacr.org/2016/260.pdf)
- [BN254 curve](https://eips.ethereum.org/EIPS/eip-197)
