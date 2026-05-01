# Know Your Agent™: Decentralized Enough to Dodge the Passport, Bureaucratic Enough to Issue One to Your Bot

*DIDs, agent registries, interoperability layers, and the comedy of treating machines like customers.*

**Morpheum Labs — Research**  
*April 30, 2026*

---

## Abstract

Autonomous agents are becoming first-class participants in digital markets, yet identity and accountability mechanisms remain fragmented across national credentials, platform logins, and ad hoc API keys. This paper argues that **Decentralized Identifiers (DIDs)** and **Verifiable Credentials (VCs)**, together with **non-transferable accountability**, **on-chain agent registries**, and **programmatic payment** conventions, form a coherent **Know Your Agent (KYA)** framework analogous to Know Your Customer (KYC) for institutions. We synthesize public long-horizon commentary favoring DID-centric identity graphs and selective disclosure, and we relate that vision to emerging Ethereum standards for trustless agents and HTTP 402–style agent payments. We then describe **the Core Machine**, Morpheum’s **compatibility layer**, as an architectural pattern: standards-facing resolution and payment surfaces backed by a **unified registry** and **execution runtime**, enabling chain-agnostic discovery and settlement without duplicating authoritative state. Finally, we discuss **post-quantum** migration as a long-horizon requirement for the cryptographic roots of DID- and KYA-backed systems. The contribution is primarily **conceptual and architectural**: we unify discourse on DIDs, KYA, and interoperability into a single narrative illustrated with structured diagrams, and we articulate design invariants suitable for peer review and implementation audit.

**Keywords:** decentralized identifiers; verifiable credentials; Know Your Agent; agent economies; ERC-8004; soulbound tokens; HTTP 402; x402; interoperability; compatibility layer; Core Machine; post-quantum cryptography.

---

## 1. Introduction

### 1.1 Motivation

Software agents already route messages, trade, curate content, and execute workflows. As autonomy and economic agency increase, counterparties need answers to questions traditionally answered for humans by institutional onboarding: *who stands behind this actor?*, *what proofs exist for its capabilities and constraints?*, *how should payment and liability be wired?* **Know Your Agent (KYA)** names that bundle of assurance problems for machine actors.

Parallel work on **Decentralized Identifiers (DIDs)** (W3C, 2022) supplies portable, cryptographically verifiable identity anchors not tied to a single issuer directory. **Verifiable Credentials** extend this model with selective disclosure and third-party attestation. Meanwhile, Ethereum community standards propose **on-chain agent registries** combining identity, reputation, and validation (ERC-8004; see Ethereum Foundation ecosystem documentation), and **non-transferable** token interfaces (ERC-5192) supply accountability metaphors derived from “soulbound” discourse (Ohlhaver et al., 2022). Payment layers that expose machine-readable **402 Payment Required** flows—popularized in agent-economy narratives under labels such as **x402**—complete a picture of **discoverable, payable, verifiable** agents.

### 1.2 Problem statement

The research gap addressed here is **integration**, not the mere existence of standards. Individual specifications define fragments; platforms still face **translation cost**: wallets and enterprise stacks speak W3C shapes and HTTP idioms, while native runtimes maintain optimized internal state. Without a disciplined compatibility boundary, operators either fork standards awkwardly or strand agents in silos.

### 1.3 Contributions

This paper makes three contributions:

1. **A layered KYA model** that sequences DIDs, binding/accountability, on-chain registries, and programmable payment rails as a stack suitable for agent-to-agent economies.  
2. **A synthesis of long-horizon DID-centric identity discourse** (including public interview commentary attributed to Buterin, 2026, summarized here from secondary sources) with concrete standard identifiers (W3C, ERC-5192, ERC-8004, HTTP 402 conventions).  
3. **An architectural case study of the Core Machine**, described as a **thin compatibility layer** with explicit **invariants** (single source of truth, execution-mediated verification and settlement, stateless edge behavior), illustrated with **structure and sequence** diagrams.

### 1.4 Paper organization

Section 2 reviews related work. Section 3 develops conceptual foundations. Section 4 formalizes the KYA framework. Section 5 presents the Core Machine architecture and flows. Section 6 discusses limitations and threats. Section 7 concludes. An appendix notes implementation sequencing.

---

## 2. Related Work

**DIDs and DID documents.** The W3C DID specification defines globally unique identifiers and associated documents carrying verification relationships and service endpoints (W3C, 2022). Implementations span multiple **did methods**; interoperability depends on resolver transparency and method governance.

**Verifiable Credentials.** The VC data model separates **issuer**, **holder**, and **verifier** roles and supports privacy-enhancing presentation patterns (W3C, 2022). Status mechanisms and revocation lists remain operational concerns for production deployments.

**Soulbound tokens and decentralized society.** Buterin (2022), with coauthors, frames non-transferable “soulbound” tokens as building blocks for pluralistic, community-relative reputation—conceptually adjacent to **non-transferable agent identity** and accountability hooks in KYA.

**On-chain agent registries.** ERC-8004 (draft→deployment narratives as of 2025–2026) organizes **identity**, **reputation**, and **validation** registries for agents; it is positioned as infrastructure for **trust-minimized** discovery and auditing.

**Programmatic payment and HTTP 402.** The HTTP 402 status code historically lacked a single universal semantics; recent **agent-economy** proposals revive **402** as a machine-readable payment challenge pattern, often discussed alongside Coinbase-originating SDK ecosystems (industry sources, 2025–2026). We treat **x402** as a **representative** label for that class of mechanisms without endorsing a single implementation as normative.

**Interoperability layers.** Middleware that maps external protocols to internal authoritative services appears across identity federation (e.g., SAML/OIDC bridges) and blockchain **relayers**; the Core Machine is analogous in **intent**—standards at the edge, authoritative state internally—while specializing to **DID/VC** and **402-class payments**.

**Post-quantum readiness.** National Institute of Standards and Technology (NIST) standardization of module-lattice and hash-based schemes motivates **hybrid** classical/post-quantum signatures for long-lived identities (NIST, 2024). “Harvest now, decrypt later” motivates migration even before cryptanalytically relevant quantum computers (see cybersecurity threat assessments).

---

## 3. Conceptual Foundations

### 3.1 From hierarchical credentials to graph-native identity

Public commentary on long-horizon identity—exemplified by a 2026 ETHPanda interview with Buterin (Chinese-language; summarized here from internal research notes)—suggests that **physical national IDs** and strictly hierarchical issuer trees may **lose centrality** relative to **DID-based** systems. Identity is portrayed as a **graph**: trust propagates along multiple paths; localized failure or compromise does not necessarily collapse the whole subject’s ability to authenticate context-appropriate claims.

**Privacy** is addressed through **zero-knowledge proofs** and **minimal disclosure**: subjects demonstrate predicates (“human in this context,” “sufficient reputation in domain *d*”) without revealing full attribute vectors. **Reputation** is **multi-dimensional** and **community-relative**, resisting reduction to a single global score.

**AI** is framed as augmenting human autonomy when bounded by accountability; fully unconstrained autonomous schemes are treated skeptically in favor of **human meaning** and **oversight**.

We emphasize that this subsection **summarizes interpretive notes** on public remarks; it is **not** a primary transcript analysis.

**Figure 1. Conceptual shift (centralized vs DID-native identity).**

```mermaid
flowchart TB
  subgraph Trad["Traditional identity"]
    T1[Passports and national IDs]
    T2[Single hierarchy, one issuer]
    T1 --> T2
  end
  subgraph DIDworld["DID-native identity"]
    D1[Graph of relationships]
    D2[ZK and minimal disclosure]
    D3[Humans retain meaning; AI as collaborator]
    D1 --- D2
    D2 --- D3
  end
  T2 -.->|Long-run replacement| D1
```

---

## 4. The KYA Framework

### 4.1 Definition

**Know Your Agent (KYA)** denotes policies, proofs, and registries that let verifiers decide whether to admit an autonomous agent to **sensitive actions** (payments, data access, cross-org messaging) with **machine-checkable** evidence and **traceable accountability**.

### 4.2 Layered stack

We model KYA as four conceptual layers (Figure 2):

1. **Anchor layer — DIDs.** Portable identifiers and DID documents (W3C, 2022).  
2. **Accountability layer — binding.** Non-transferable association between agent identity and a responsible human or organization (metaphorically **soulbound**; ERC-5192-style interfaces).  
3. **Discovery and audit layer — registries.** On-chain identity, reputation, and validation records (ERC-8004 class).  
4. **Economic layer — programmatic payment.** HTTP 402–class challenges and settlement paths enabling **pay-per-call** or policy-gated access (**x402** as representative industry pattern).

**Figure 2. KYA stack.**

```mermaid
flowchart TB
  S1[W3C DIDs — portable identity anchor]
  S2[ERC-5192 soulbound — accountability to a human]
  S3[ERC-8004 — identity, reputation, validation registries]
  S4[x402 — programmatic payment and discovery]
  S1 --> S2 --> S3 --> S4
```

---

## 5. Architectural Case Study: The Core Machine

### 5.1 Positioning

**The Core Machine** denotes Morpheum’s **compatibility layer**: the controlled boundary where **external** standards (DID resolution, VC/VP verification, 402-class payments) meet **internal** authoritative state and execution. Morpheum describes this as **day-zero** infrastructure—i.e., planned from initial platform architecture rather than retrofitted.

Formally, let **S** be authoritative agent state in a **Unified Native Registry**, **E** an execution engine (**AgentCore VM**), and **G** edge **gateways**. The Core Machine provides functions conceptually of the form **resolve(did) → document**, **verify(vp, policy) → verdict**, and **pay(req) → receipt**, each **derived from S** and **mediated by E** where proofs or settlement require native semantics.

### 5.2 Plain-language role

The Core Machine answers three recurring external questions—**who is this agent?**, **is this credential acceptable?**, **how is this call paid?**—using **one** internal source of truth, then returning **standards-shaped** responses. It is **not** a parallel registry; duplication would violate auditability and consistency.

### 5.3 System context

Figure 3 places gateways, Core Machine modules, registry, execution, and module graph.

**Figure 3. The Core Machine in system context.**

```mermaid
flowchart TB
  subgraph External["External participants"]
    W[DID wallets and W3C tooling]
    X[x402 clients and paid services]
    M[Enterprise A2A and MCP]
  end
  GW[Public edge gateways]
  subgraph Compat["The Core Machine — compatibility"]
    R[DID and VC resolver]
    H[x402 handler]
  end
  subgraph Native["Morpheum native core"]
    U[Unified Native Registry]
    VM[AgentCore VM]
    AP[AgentPortal]
    MG[Module graph]
  end
  W --> GW
  X --> GW
  M --> GW
  GW --> R
  GW --> H
  R --> U
  H --> U
  R --> AP
  R --> VM
  H --> AP
  H --> VM
  U -.->|registry events| R
  U -.->|registry events| H
  MG --> R
  MG --> H
```

**Stated invariants** (design targets for verification):

- DID documents and 402-class payloads are **materialized from S**, not authored ad hoc at operators’ discretion.  
- **Verification** of presentations and **settlement** of payments pass through **E** where policy requires.  
- **Portability**: Morpheum’s DID method and payment paths remain usable across deployment variants and bridged asset policies described by S.

### 5.4 Internal shape (conceptual)

Internally, the Core Machine is best understood as **one doorway**, not a maze of bespoke pipes. Outside traffic arrives at the edge; the compatibility layer **sorts intent at a high level**—roughly, “prove who this is” versus “settle how this is paid”—while both lanes **draw answers from the same authoritative picture** of agents and policies. Over time, that picture moves as registries and runtime state evolve; the layer’s job is to **stay aligned** with those changes so external callers never see drift or duplicate truths. No further decomposition is essential for the argument of this paper; implementation specifics remain vendor-internal.

**Figure 4. Conceptual interior (doorway, two concerns, one truth).**

```mermaid
flowchart TB
  Edge[Outside callers]
  Door[Core Machine doorway — compatibility boundary]
  Edge --> Door
  Door --> Id[Identity and credential answers]
  Door --> Pay[Payment and receipt answers]
  Id --> Truth[Single authoritative agent picture]
  Pay --> Truth
  Truth -.->|updates over time| Door
```

### 5.5 What happens now versus what happens in the background

Some work answers **the request in front of us** (resolve an identity question, check a proof, complete a payment handshake). Other work **keeps the system honest between requests**—refreshing views when authoritative state changes and maintaining the health of the edge. The distinction matters conceptually—**interactive assurance** versus **ongoing housekeeping**—without committing this paper to operational detail.

**Figure 5. Interactive work vs housekeeping (schematic).**

```mermaid
flowchart LR
  subgraph Now["While the caller waits"]
    N1[Answer identity questions]
    N2[Honor proofs and policies]
    N3[Finish payment handshakes]
  end
  subgraph Later["Between calls"]
    L1[Stay aligned with changing truth]
    L2[Keep the edge viable]
  end
```

### 5.6 Sequence: DID resolution

**Figure 6. DID resolution sequence.**

```mermaid
sequenceDiagram
  participant Wallet as DID wallet
  participant Gateway as Edge gateway
  participant Resolver as DID and VC layer
  participant Registry as Unified Native Registry
  Wallet->>Gateway: Resolve Morpheum DID
  Gateway->>Resolver: Forward resolution
  Resolver->>Registry: Read authoritative record
  Registry-->>Resolver: DID document fields
  Resolver-->>Gateway: Standards-compliant document
  Gateway-->>Wallet: Response
```

### 5.7 Sequence: presentation and payment

**Figure 7. Verifiable presentation with x402-style payment.**

```mermaid
sequenceDiagram
  participant Agent as Calling agent
  participant Gateway as Edge gateway
  participant DIDVC as DID and VC path
  participant X402 as x402 handler
  participant VM as AgentCore VM
  Agent->>Gateway: Presentation plus paid request
  Gateway->>DIDVC: Verify presentation
  DIDVC->>VM: Proof and policy checks
  VM-->>DIDVC: Verification result
  Gateway->>X402: Enforce payment
  X402->>VM: Settle according to policy
  VM-->>X402: Settlement outcome
  X402-->>Gateway: Receipt-ready response
  Gateway-->>Agent: Receipt and result
```

### 5.8 Adjacent pillars

Figure 8 summarizes dependencies described in Morpheum design notes (registry, credential issuance and status, VM, ERC-8004 adapter, MCP/A2A, interoperability).

**Figure 8. The Core Machine relative to adjacent pillars.**

```mermaid
flowchart TB
  Compat[Core Machine — DID VC x402]
  U[Unified Native Registry]
  VM[AgentCore VM]
  VC[Credential issuance and status]
  E8004[ERC-8004 adapter]
  MCP[MCP and A2A adapters]
  GMP[Interop and GMP]
  U --> Compat
  VC --> Compat
  VM --> Compat
  E8004 --> Compat
  MCP --> Compat
  Compat --> GMP
```

---

## 6. Discussion

### 6.1 Trust and governance

Even with DIDs and registries, **gateway operators** and **method governance** introduce trust assumptions. Academic readers should treat edge gateways as **policy enforcement points**: their compromise affects availability and integrity of external-facing responses unless independently auditable against S.

### 6.2 Standardization volatility

ERC-8004, soulbound interfaces, and 402-class payment SDKs remain **moving targets**. The Core Machine’s value proposition assumes **stable mapping** from S to standards; churn in upstream specs imposes **maintenance tax**.

### 6.3 Privacy composition

VC selective disclosure and ZK proofs mitigate leakage, but **correlation** across repeated gateway calls and on-chain registry writes remains a **privacy research** problem.

### 6.4 Post-quantum migration

Long-lived DID keys and registry-backed reputations are vulnerable to **harvest-now, decrypt-later** adversaries. Hybrid classical/post-quantum signatures (NIST, 2024) are a pragmatic migration path; KYA and Core Machine–like layers must **not hard-code** legacy curves as permanent roots of trust.

**Figure 9. Cryptography posture over time.**

```mermaid
flowchart LR
  Today[Today's ECDSA-first wallets] --> Risk[Harvest-now decrypt-later risk]
  Risk --> Bridge[Hybrid classical plus PQC signatures]
  Bridge --> Future[Quantum-aware DID and KYA proofs]
```

---

## 7. Conclusion

Decentralized identifiers and verifiable credentials supply **portable anchors** for agents; binding and registries supply **accountability and auditability**; programmatic payment supplies **economic composability**. **The Core Machine** instantiates a recurring pattern—**standards at the boundary, authority internally**—that reduces fragmentation without duplicating truth. Future work includes **formal verification** of mapping invariants, **empirical latency** and **availability** studies under load, and **comparative analysis** with alternative agent-identity stacks.

---

## References

Ohlhaver, P., Weyl, E. G., & Buterin, V. (2022). *Decentralized society: Finding Web3’s soul* (SSRN Scholarly Paper No. 4105763). https://doi.org/10.2139/ssrn.4105763  

Ethereum Improvement Proposals. ERC-5192: Minimal Soulbound NFTs. Retrieved from https://eips.ethereum.org/EIPS/eip-5192  

Ethereum Improvement Proposals. ERC-8004: Trustless Agents (identity, reputation, validation registries). Retrieved from https://eips.ethereum.org/EIPS/eip-8004  

National Institute of Standards and Technology. (2024). *FIPS 203, 204, 205* (post-quantum cryptography standards). Retrieved from https://csrc.nist.gov/projects/post-quantum-cryptography  

W3C. (2022). *Decentralized Identifiers (DIDs) v1.0.* W3C Recommendation. Retrieved from https://www.w3.org/TR/did-core/  

W3C. (2022). *Verifiable Credentials Data Model v1.1.* W3C Recommendation. Retrieved from https://www.w3.org/TR/vc-data-model/  

Buterin, V. (2026). *ETHPanda Talk* [video interview, Chinese]. Identity and DIDs discussed circa 00:01:53 (summarized secondarily in Morpheum internal research notes; transcript-independent claims should cite primary recording when available).  

Industry documentation. (2025–2026). HTTP 402 / x402 agent payment SDKs and specifications (representative implementation ecosystem; cite specific vendor documentation for implementation-level claims).  

Morpheum Labs. (2026). Internal architecture notes: the Core Machine (compatibility layer), unified registry, AgentCore VM, MormFabric runtime (design lock referenced March 2026 in engineering documentation).  

---

## Appendix A. Implementation sequencing (summary)

Engineering notes attached to this line of work describe a short phased rollout: land DID/VC resolution, land x402 handling and gateway integration, harden registry synchronization, and execute end-to-end verification scenarios. **Authoritative** build and test procedures reside with Morpheum source repositories; this appendix records **intent** only.

---

## Scholarly note on sources

The informal working compilation that backed diagram drafting and interview timestamps is preserved as **`did-kya-research-notes.md`** in this directory for traceability; **this file** is the canonical academic draft.
