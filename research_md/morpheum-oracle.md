## Overview

This note sketches the **Morpheum Oracle Engine**—the pricing and risk layer behind the Morpheum DEX. The exchange is built around a **Directed Acyclic Graph (DAG)** design described as gasless, blockless, and not routed through classic smart-contract call paths for its core matching. In plain terms, the oracle stack is meant to supply **fast, aggregated prices**, **risk controls**, and **auto-deleveraging** for perpetuals, options, and spot.

The core idea, in one sentence: treat oracle ingestion, normalization, and publication as a **pipeline** with clear boundaries so latency budgets and failure modes stay inspectable.

---

## Why it matters

If a perpetuals venue mis-marks or stalls during stress, the failure mode is rarely “wrong UI”—it is liquidations, basis blowouts, and brittle automation downstream. A layered oracle design matters because it lets operators reason about **which source failed**, **what fallback applied**, and **how stale** each feed was when a decision was made.

---

## System architecture and data flow

The described pipeline has three layers:

- **Input**: Centralized venues (e.g. Binance, OKX), external oracles (Chainlink, Pyth), internal daemons, and other services.
- **Process**: TWAP-style smoothing, funding and mark conventions (including HMIB-style marks), spreads, **ADL** with LP-favoring rules, and aggregation across sources.
- **Output**: REST and gRPC, WebSockets, Prometheus, and PostgreSQL for history and audit surfaces.

The documentation quotes aggressive latency targets (for example **<50 ms** API responses and **<1 ms** for “hot path” calculations) and claims substantial efficiency wins from unifying market-data structures (on the order of **~87.5%** memory reduction and **~60–70%** performance improvement versus a prior fragmented layout). Those numbers should be read as **reported engineering outcomes for a specific refactor**, not as universal laws—reproduce them on your workload if they matter for capacity planning.

---

## Version note (v1.2.911)

Reported changes include: unified market-data internals, heartbeat fixes aimed at **7/7** healthy daemons, multi-source failover ordering (**CEX → Pyth → Chainlink**), and richer options inputs (Deribit / OKX / Binance) with aggregation, Greeks, and Mark IV.

---

## Sharding and DAG integration

**Sharding** splits the **CLOB** (matching) and **risk engine** (PNL, liquidations, bucket IDs) across shards on the DAG. Placement is described as a **greedy**, capacity-aware allocation so larger nodes absorb proportionally more load; **sub-DAGs** per shard support concurrent work, and **blobs** hold short-lived batch-like data with expiry.

Published throughput and latency figures (**~5k–10k TPS**, **200–500 ms** end-to-end style latency, **<1 s** recovery with **3–5×** replication) are **design targets and reported ranges**. In the least convenient world—partitioning, poisoned feeds, or hotspot keys—those headlines need tracing back to measured percentiles and failure injections.

---

## Feature areas (with trade-offs)

### Multi-source aggregation and failover

Inputs are merged with weights, outlier and staleness filters, and an ordered fallback path. The upside is **robustness to single-feed gaps**; the downside is **complexity**: failover policies can hide slowly drifting bias unless monitoring covers **per-source divergence** and **cache age**.

### Sharding for heterogeneous hardware

User-ID hashing spreads positions and blob state; replication and greedy shard sizing aim to cap imbalance (documentation cites bounds like **max(lᵢ/sᵢ) ≤ 2 × OPT** under their model). This is plausible for steady load, but **skewed whales** or **correlated bursts** can still stress individual shards—something only production traces settle.

### Risk: ADL and buckets

**Auto-deleveraging** with LP-favoring ranking, bucket IDs for risk bands, and a sharded risk engine is a standard pattern for **parallel liquidation math**; the social and game-theoretic properties of ADL (who gets closed and when) remain contentious across venues—implementation correctness does not resolve incentive debates.

### Observability

Prometheus metrics, heartbeats, hot-reload configuration, and graceful degradation are framed toward **99.9%** uptime. Uptime claims are best validated against **your** SLO definitions (what counts as “down,” and for whom).

---

## Efficiency and scalability (stated vs. inferred)

For **10–20 M** positions, the design points toward **~100–200** shards and **~50–100** nodes, with horizontal scaling and Merkle- or fraud-proof style integrity mechanisms in the broader system story. Comparisons to centralized venues (Binance-scale position counts, six-figure TPS) are **illustrative context**; decentralization buys different failure modes, not a strict dominance ordering on every metric.

---

## Synthesis

On balance, the Morpheum oracle narrative is **coherent as an engineering brief**: fuse many feeds, normalize aggressively, shard heavy state, and expose observability so operators can see degradation before users do. The open questions—how failover interacts with **latency SLOs**, how **options IV** quality tracks under thin markets, and how **ACT-like** or **agent-driven** traffic spikes interact with shard placement—are where I would spend validation effort next.

The system is described as **production-oriented** (specific version cited above); whether it matches *your* production bar still depends on audits, incident history, and reproducible benchmarks rather than the headline numbers alone.
