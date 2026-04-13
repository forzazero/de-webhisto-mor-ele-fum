## Overview

The documents describe the Morpheum Oracle Engine, a core component of the Morpheum DEX—a decentralized exchange built on a Directed Acyclic Graph (DAG) architecture that is gasless, blockless, and non-smart contract-based. It focuses on high-performance price oracles, risk management, and auto-deleveraging for perpetuals, options, and spot markets.

## System Architecture and Data Flow

The system follows a layered pipeline:

- **Input Layer**: Ingests data from CEXs like Binance/OKX, oracles like Chainlink/Pyth, market daemons, and internal services
- **Process Layer**: Handles TWAP, funding rates, HMIB mark prices, spreads, ADL with LP favoritism, and multi-source aggregation
- **Output Layer**: Delivers via REST/gRPC APIs, WebSockets, Prometheus metrics, and PostgreSQL storage

Market IDs flow end-to-end with low latency (<50ms API responses, <1ms calculations), supported by unified market data services (87.5% memory reduction, 60-70% performance boost via single data structures and adapters).

## Key Enhancements (v1.2.911)

- Unified market data refactoring
- Heartbeat fixes for daemons (ensuring 7/7 healthy)
- Multi-source reliability (CEX → Pyth → Chainlink failover)
- Options data sourcing from Deribit/OKX/Binance with aggregation, Greeks, and Mark IV volatility calculations

## Sharding and DAG Integration

Sharding partitions the CLOB (order matching) and risk engine (PNL, liquidations, bucket IDs) across shards in the DAG network. Data distribution uses a greedy algorithm based on node sizes (capacity-normalized allocation to balance load), with sub-DAGs per shard for concurrent processing. Blobs handle temporary data (e.g., rollup-like batches) with expiry.

This enables ~5,000-10,000 TPS, 200-500ms latency, and fault tolerance (<1s recovery via 3-5x replication).

## Innovative Features

### 1. Multi-Source Oracle Aggregation with Automatic Failover

Unified input layer aggregates data from CEXs (Binance, OKX, etc.), Pyth (high-frequency feeds), and Chainlink (decentralized on-chain), with weighted averages, outlier detection, staleness filtering, and priority failover (CEX → Pyth → Chainlink).

**Innovation**: 87.5% memory reduction via single data structures/adapters; 90%+ cache hit rate with predictive TTL; supports HMIB mark prices, EMA-smoothed funding rates, and Mark IV volatility for options.

### 2. Sharding Optimized for Heterogeneous Nodes

Shards CLOB/risk engine by user ID hash, distributing data (positions, blobs) greedily based on node sizes (capacity-normalized: larger nodes get bigger shards), with 3-5x replication and <1s failure recovery.

Handles 10-20M positions with proportional load (e.g., ~100k/shard), using blobs for temporary data (expiry ~1 hour) and sub-DAGs for intra-shard concurrency; minimizes imbalance (max(l_i / s_i) ≤ 2 × OPT).

### 3. Advanced Risk Management

ADL (auto-deleveraging) prioritizes liquidity providers (LP favoritism) in position ranking/execution; uses bucket IDs for risk categorization (PNL/liquidations); integrates with sharded risk engine for parallel processing.

Event-driven, with insurance fund integration and deficit coverage; supports cross-margin funding and inverse/quanto perps.

### 4. Production-Ready Observability

Comprehensive Prometheus metrics (e.g., cache hits, failover rates), heartbeat health checks (7/7 daemons), graceful degradation, and hot-reload configs without restarts. 99.9% uptime with circuit breakers and historical backfill; unified metrics for all layers.

## Efficiency and Scalability

For 10-20M positions, the design scales horizontally (100-200 shards, 50-100 nodes), with high TPS/low latency, proportional storage (~10-20 GB total), and security (Merkle proofs, fraud proofs). It's compared favorably to centralized exchanges (Binance: ~8M positions, >100k TPS; OKX/Bybit: similar), but adds decentralization.

Production-ready with monitoring, hot-reload configs, and Prometheus metrics. Emphasizes reliability (99.9% uptime, self-healing), accuracy (outlier detection, confidence weighting), and scalability (horizontal, event-triggered DAG updates).

## Conclusion

The system is production-ready (v1.2.911), focused on DEX oracle/risk needs, with an emphasis on multi-source data fusion, sharding for heterogeneity, and DAG for concurrency—making it suitable for high-frequency trading while addressing blockchain scalability challenges.
