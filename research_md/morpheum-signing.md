## Hybrid ECDSA and ML-DSA-44 signatures for Morpheum agents

Classical elliptic-curve signatures—**ECDSA** is the familiar example on chains like Bitcoin and Ethereum—rest on problems we believe are hard for **classical** computers. **Shor’s algorithm**, run on a sufficiently large and fault-tolerant quantum machine, would break discrete-log hardness in polynomial time, which would break ECDSA-style schemes as usually deployed. **Grover’s algorithm** tightens the story for symmetric primitives in a gentler way (effectively halving effective bit-strength in a rough accounting).

None of this implies that “everything collapses next Tuesday.” Timelines for **cryptographically relevant quantum computers** are uncertain; some public estimates cluster in the **2030s**, others are more conservative. The honest posture is **risk management under ambiguity**: migrate critical long-lived assurances with enough lead time that ecosystems are not forced into panic retrofits.

Morpheum describes a concrete mitigation for its agent layer: a **hybrid** composite signature, **`ECDSA_MLDSA44`**, pairing ECDSA with **ML-DSA-44** (the smallest parameter set from NIST’s module-lattice family, standardized in **FIPS 204**). Agent addresses use the **`mr4m1`** prefix in the documented design. I think of this less as a dramatic brand claim and more as a **prudent layered bet**: keep today’s interoperability, add a **post-quantum** anchor, and preserve room to swap parameter sets if standards evolve.

---

### The quantum threat in plain terms

**Shor** targets hidden subgroup structures that underpin RSA and (standard) elliptic-curve discrete logs; **ECDSA** on common curves is in the crosshairs of that story in principle. **ML-DSA** (historically **CRYSTALS-Dilithium**) is built around problems such as **module learning with errors (MLWE)** for which we do not currently have a Shor-like shortcut—though “no known algorithm” is always provisional in cryptography.

NIST specifies ML-DSA at three strengths (**44 / 65 / 87**). Morpheum’s docs emphasize **ML-DSA-44**, trading a smaller footprint and faster operations against a lower NIST level than **-65** or **-87**. That is a familiar engineering trade-off: not every deployment needs the heaviest parameter set if latency and bandwidth dominate.

---

### Why hybrid?

Replacing ECDSA overnight across tooling, audits, and hardware security modules would be socially and operationally costly. A **composite** signature—both components must verify—mirrors guidance you see from **NIST** and **IETF** work on PQ composites (e.g. lamps drafts on composite signatures):  

1. **Backward compatibility** with existing ECDSA verifiers and workflows where the classical half suffices for transitional policy.  
2. **PQ anchor** so that a future quantum adversary cannot forge merely by breaking ECDSA—provided the composite policy truly requires **both** halves.  
3. **Agility**: if ML-DSA parameters age poorly, you can rotate **within** the PQ half without pretending the whole world moved in one step.

**Defense-in-depth** should be stated carefully. If **ECDSA** fails classically, the PQ half still matters; if **ML-DSA** were broken, ECDSA might still buy time **until** a quantum adversary appears—but a classical break of ML-DSA would still be a serious incident for any verifier that trusted the PQ half alone on other paths. Composites help **migration**; they do not magically erase all interaction effects between policies.

In the documented Morpheum flow, both signatures cover the same message digest; verification accepts only if **both** validate. **ML-DSA-44** signatures are much larger than ECDSA (~**2.4 KB** vs ~**71 B** on typical secp256k1 ECDSA signatures in the doc’s rounding)—bandwidth and verification CPU move from “negligible” to “something you measure,” especially at high fan-out.

---

### How Morpheum wires this for agents

From the project’s specifications (signing, delegation, auth):

- Agent addresses: **`mr4m1`** prefix; hybrid **`ECDSA_MLDSA44`**.  
- Used where delegation, **Trading Key** claims, and high-frequency agent operations need long-lived trust assumptions.  
- Integrated with **EIP-712** typed data, nonce handling (**TBHWM v2.0**), and lifecycle calls such as **`agent::approve`** / **`agent::revoke`**.

For autonomous or delegated trading, the signature layer is part of a larger story—**Verifiable Credentials**, portal nodes, caching—that can fail for non-crypto reasons (replay bugs, policy mistakes, compromised endpoints). Post-quantum signatures reduce **one** class of tail risk; they do not replace careful operational security.

---

### ML-DSA-44 details (FIPS 204)

**ML-DSA-44** is the efficient parameter set finalized in **FIPS 204 (August 2024)** at roughly **NIST level 2** (~**128-bit** classical and quantum strength in NIST’s categorization narrative).

#### 1. Cryptographic core

- **Hard problems**: **MLWE** and **SelfTargetMSIS** on module lattices.  
- **Ring**: \( R_q = \mathbb{Z}_q[X] / (X^{256} + 1) \), \( q = 8380417 \) (NTT-friendly prime).  
- **Paradigm**: Fiat–Shamir with aborts (rejection sampling).  
- **Goal**: **SUF-CMA** unforgeability in the standard model framing of the standard.

#### 2. Parameters (FIPS 204 Table 1)

| Parameter       | Value          | Meaning |
|-----------------|----------------|---------|
| **k**           | 4              | Rows in public matrix A |
| **l**           | 4              | Columns in public matrix A |
| **q**           | 8380417        | Modulus (prime) |
| **η**           | 2              | Secret key coefficient bound (s1, s2 ∈ [-η, η]) |
| **τ**           | 39             | Number of ±1 coefficients in challenge polynomial c |
| **β**           | 78             | Bound used in rejection sampling |
| **γ₁**          | 2¹⁷ = 131072   | Bound for masking vector y |
| **γ₂**          | (q-1)/88 ≈ 95232 | Low-order rounding range |
| **ω**           | 80             | Maximum number of 1s allowed in hint vector h |
| **d**           | 13             | Number of bits dropped from t (public key compression) |
| **Claimed Security** | Category 2 (≈128-bit) | NIST Level 2 |

#### 3. Sizes (ML-DSA-44)

- **Public key**: **1,312 bytes**  
- **Secret key**: **2,560 bytes**  
- **Signature**: **2,420 bytes**  

(Contrast: ECDSA on secp256k1 is far smaller for keys and signatures in typical encodings.)

#### 4. Algorithm sketch

##### Key generation (deterministic from a 32-byte seed)

1. Sample **ρ** (expand **A**).  
2. Sample **K** (masking).  
3. **A** ← ExpandA(**ρ**).  
4. Sample short secrets **s₁**, **s₂**.  
5. **t** = **A·s₁** + **s₂**; split into **t₁**, **t₀** (drop **d** low bits).  
6. **pk** = (**ρ**, **t₁**); **sk** includes (**ρ**, **K**, **tr** = Hash(**pk**), **s₁**, **s₂**, **t₀**).

##### Signing (with rejection)

Typical implementations loop until acceptance (~**4–6** tries on average for this parameter set in published benchmarks—your mileage varies with implementation and side-channel mitigations).

1. **μ** = H(**tr** ‖ **M**).  
2. Sample masking; iterate: draw **y**, compute **w**, derive sparse challenge **c**, form **z**, check norms and hints; **reject** or **accept**.  
3. Output packed **σ** = (**c**, **z** mod **q**, **h**).

##### Verification

Recompute **w₁′**, **c′**, and check equality and bounds. Well-written verifiers aim for **constant-time** comparisons on secrets’ derived values; ML-DSA verification is often competitive with ECDSA on modern CPUs thanks to **NTT** engineering.

#### 5. Performance

Signing pays for **lattice arithmetic** and **retries**; verification is usually the easier budget item in high-throughput servers—but **always profile your binary**.

#### 6. Why ML-DSA-44 here?

It is the **lightest** standardized ML-DSA tier Morpheum cites for **agent-native** traffic; pairing it with ECDSA is the documented composite. If regulations or internal policy later demand **-65** or **-87**, that is a straightforward knob **if** clients and verifiers agree.

---

### Contrast: hardware QKD vs. software PQ signatures

Projects like the **“Quantum Lock”** prototype (Stevens Institute, ~2019–2020 press coverage) illustrate **QKD-style** physical keying for constrained links. QKD can offer appealing assumptions **between fixed endpoints**; it wrestles with distance, trusted relays, and cost.

Morpheum’s setting—**many verifiers**, **permissionless replication**, **software updates**—maps more naturally to **algorithm agility** than to fiber-linked hardware modules. The two ideas solve **different layers** of the problem; neither renders the other pointless.

---

### Production concerns worth keeping explicit

Documentation mentions **sub-100 µs** portal-scale verification budgets and zero-copy-friendly layouts alongside caches and pinning. Those claims are **plausible for tuned Rust services**, but they depend on CPU models, batching, and whether verification sits on the critical path every hop.

Hybrid deployments exist elsewhere (**AWS KMS** support for ML-DSA, X.509 composite experiments, Ethereum research threads). “Others did it” is comforting for **library maturity**, not a substitute for **your** integration tests.

---

### Closing synthesis

On balance, **ECDSA + ML-DSA-44** for **`mr4m1`** agents is a **reasonable migration-shaped choice**: standards-aligned PQ primitive, classical half for tooling continuity, composite verification policy. The honest limitations are **size**, **signing latency**, **implementation risk**, and the usual caveat that **standards and assumptions shift**.

Open questions for any deployment: **Where is hybrid verification mandatory vs. optional?** **How do light clients handle 2.4 KB signatures?** **What is the rotation story if ML-DSA parameters change?** Grounded engineering answers matter more than narrative certainty.

---

*References*

- Morpheum design documents: `signature_types.md`, `agent_delegation.md`, `signing.md`, `auth.md` (Feb 2026).  
- NIST FIPS 204 (ML-DSA / CRYSTALS-Dilithium).  
- “Quantum Lock” coverage (e.g. HackerNoon, 2020) — Stevens Institute prototype context.  
- IETF composite ML-DSA drafts; Bitcoin/Ethereum PQ migration discussions (2024–2026).
