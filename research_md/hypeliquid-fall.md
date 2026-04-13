### Hyperliquid Design Architecture Overview

Hyperliquid is a custom Layer 1 (L1) blockchain designed primarily as a high-performance platform for decentralized finance (DeFi), with a focus on perpetual futures (perps) trading, spot markets, and broader financial applications. It combines specialized native components for trading efficiency with a general-purpose execution environment for programmability. The architecture is built to address the limitations of traditional blockchains like Ethereum, such as low throughput, high latency, and fragmented liquidity, while aiming for a "fully on-chain open financial system." Below, I'll break down the key components based on official documentation, technical analyses, and ecosystem discussions.

#### Core Components
Hyperliquid's architecture is divided into two main interconnected layers: **HyperCore** and **HyperEVM**, forming a unified global state secured by a custom consensus mechanism. This design allows for seamless interactions between high-performance trading primitives and programmable smart contracts, without the need for bridges, proofs, or trusted intermediaries.

- **HyperCore (Native Execution Layer)**:
  - Handles performant, non-EVM components optimized for trading.
  - Key features:
    - **Fully On-Chain Order Book and Matching Engine**: Processes orders, trades, funding payments, and liquidations directly on-chain. Supports perps with up to 50x leverage, spot trading, and auto-deleveraging (ADL) to prevent bad debt.
    - **Margin and Risk Management**: Includes portfolio margining, borrowing/lending, and liquidation mechanisms. Liquidations first attempt the order book for better prices, with backstop by the Hyperliquid Liquidity Pool (HLP), a community-owned vault.
    - **Staking and Governance**: Uses the native HYPE token for staking, securing the network, and community proposals (e.g., HIP-3 for permissionless market deployments).
    - **Oracles**: Validator-operated for core perps (weighted median from major CEXs); HIP-3 markets allow custom oracles (e.g., via MPC or Pyth Network).
    - **Multi-Sig and Bridges**: Manages assets like USDC via an Arbitrum bridge (being deprecated for native USDC minting) and multi-sig controls for security.
  - State Management: HyperCore maintains margin, matching engine state, and liquidity pools. All trades are verifiable on-chain in real-time.
  - Performance: Designed for sub-second finality (0.07-second block times) and zero-gas trading to eliminate wallet pop-ups and delays.

- **HyperEVM (General-Purpose Layer)**:
  - An Ethereum-compatible virtual machine (EVM) for deploying smart contracts using standard tools like Solidity.
  - Integration with HyperCore: Uses "precompiles" for read/write interactions.
    - **Read Precompiles**: Smart contracts can query HyperCore data (e.g., order book prices) directly.
    - **Write Precompiles**: Contracts can execute actions on HyperCore (e.g., sending orders or swaps) as part of block execution.
  - Enables composability: Builders can deploy ERC-20 tokens, link them to HyperCore tickers for order book trading, or build lending protocols that use HyperCore liquidity for liquidations.
  - Example Workflow: Deploy an ERC-20 on HyperEVM, auction a HyperCore ticker, link them for seamless transfers and trading. This avoids bridging risks and allows permissionless listings.

- **Consensus Mechanism: HyperBFT**:
  - Custom algorithm based on HotStuff and subsequent BFT (Byzantine Fault Tolerance) improvements.
  - Properties:
    - Asynchronous: Consensus doesn't block on execution; transactions are sequenced without waiting for block hashes.
    - Optimistic Responsiveness: Block times bounded only by network delay, not fixed synchronous timers.
    - High Throughput: Supports up to 200,000 TPS (transactions per second), with current bottlenecks at ~20,000 TPS under Tendermint (prior consensus, now migrated).
    - Security: Permissionless validator set; BFT proof-of-stake ensures state verification across nodes.
  - Migration: Rolled out incrementally in 2024 for stability, then scaled for throughput.

- **Overall System Flow**:
  - Users interact via wallets or APIs; transactions are sequenced by HyperBFT validators.
  - Execution: HyperCore handles native ops (e.g., matching); HyperEVM runs contracts that can call into HyperCore.
  - Settlement: All on-chain, with real-time transparency (e.g., trades, positions, liquidations indexed by nodes).
  - Scalability: Horizontal via HIP-3 (permissionless perp deployments) and vertical via native USDC integration to replace bridges.

| Component | Purpose | Key Tech | Performance Metrics |
|-----------|---------|----------|---------------------|
| HyperCore | Native trading primitives | On-chain order book, oracles, staking | 20k-200k TPS; 0.07s blocks |
| HyperEVM | Programmable contracts | EVM-compatible; precompiles for Core integration | Unified state; no bridging |
| HyperBFT | Consensus | Custom BFT (HotStuff-based) | Asynchronous; optimistic responsiveness |
| HLP Vault | Liquidity backstop | Community-owned pool | Permissionless deposits; supports liquidations |

This architecture positions Hyperliquid as a "financial layer" rather than a general blockchain, prioritizing speed and efficiency for DeFi over broad utility.

### Evidence of Centralization

Despite its DeFi branding and claims of progressive decentralization, Hyperliquid exhibits significant centralization in its current implementation. This is not hidden— the team acknowledges it's a "race to decentralize"—but critics argue it operates more like a centralized exchange (CEX) with blockchain window dressing. Here's a substantiated breakdown:

- **Validator Centralization**:
  - Limited Set: As of early 2026, Hyperliquid runs with around 24 validators, many controlled by the team or foundation. This is far below thousands needed for robust decentralization (e.g., Ethereum has ~1M validators).
  - Control Risks: Validators can submit oracle prices (weighted median from CEXs), but a single key could theoretically override (though disputed as HIP-3-specific). Community concerns include "excessive centralization" and potential for downtime or manipulation during upgrades.
  - Examples: In 2025 incidents (e.g., POPCAT volatility, API failures), the chain froze bridges or relied on team intervention, highlighting single points of failure.

- **Governance and Control Mechanisms**:
  - Foundation Influence: The Hyper Foundation holds sway over decisions, including upgrades and fee distributions (97% of revenue buys back/burns HYPE). Critics call this a "double-edged sword," enabling speed but risking long-term centralization.
  - "Godmode" Features: Decompiled code reveals functions like CoreWriter (allows EVM contracts to send Core actions) and ModifyNonCirculatingSupply (alters token supply display). While defended as testnet-only or informational, their presence "undermines the trust model."
  - Transaction Submission: Controlled by ~8 undisclosed addresses, minimizing MEV but enabling potential censorship. Future upgrades aim for MEV-resistant, multi-proposer blocks.

- **Comparison to Competitors**:
  - Hyperliquid claims more transparency than Lighter (single centralized sequencer) or Aster (centralized matching). However, it's less decentralized than fully open chains like Solana.
  - Incidents: 2025's "Black Wednesday" exposed HLP vulnerabilities and coordinated CEX pressure, amplifying centralization fears.

- **Progressive Decentralization Plan**:
  - The team plans full open-sourcing of HyperCore (HyperEVM already is) and validator expansion. However, as of 2026, it's "not considered a CEX" but lacks open node software, raising "trust model" issues.
  - Community Response: X discussions highlight "hidden operators" and "keys that can override prices," with calls for GitHub openness.

In summary, Hyperliquid's centralization stems from team-controlled validators, closed-source elements, and intervention capabilities, making it functionally similar to a CEX despite on-chain transparency claims. This enables its speed but invites scrutiny.

### Source Code and Research Developments Visibility

Hyperliquid is not fully open-source, which limits public scrutiny of its internals and fuels centralization debates. Key points:

- **Closed-Source Core**:
  - Node and validator code remains private, preventing independent verification of execution or consensus. Only HyperEVM execution is open-source.
  - GitHub Presence: Repos like hyperliquid-dex include SDKs (e.g., Python/JS APIs) and some tools, but not the full chain code. Community forks exist for bots, but core logic is absent.

- **Research and Developments**:
  - Opaque: Internal R&D (e.g., HyperBFT optimizations, ADL algorithms) isn't publicly shared beyond high-level announcements. Disputes like the 2025 $653M ADL analysis couldn't be fully verified without code access.
  - Plan: Team states code will open when "secure," prioritizing innovation over immediate transparency. Critics argue this delays accountability.

- **Public Access**:
  - Docs and APIs: Detailed in GitBook, but no full codebase. Third-party tools (e.g., flowsurface for orderflow) rely on public APIs, not source.
  - Community Efforts: Open-source bots and SDKs exist, but they interface with, not reveal, the core system.

This opacity means independent researchers can't audit developments, aligning with user claims of "no one can see the source code." However, on-chain data (e.g., trades) is verifiable via nodes, offering partial transparency.


### Historical Major "Rekts" on Hyperliquid DEX

Hyperliquid, a Layer 1 blockchain-focused perp DEX, has experienced several notable incidents involving significant user or protocol losses (often called "rekts" in crypto slang). These include exploits, manipulations, large-scale liquidations, and user errors. Based on comprehensive research from news sources, on-chain analyses, and X discussions, I've compiled a chronological list of all major historical events up to January 18, 2026. Note that Hyperliquid emphasizes these are not always "hacks" but often result from mechanics like leverage, liquidity, or user actions. Smaller daily liquidations (e.g., individual trades under $10K) are common but not included here as "historical rekts"—focus is on events with $1M+ impact or widespread attention.

I've categorized them by type for clarity, with estimated losses, key details, and context. Total documented losses across these events exceed $100M, though much stems from user leverage rather than protocol flaws.

#### 1. **May 2024: GCR Account Compromise and ETHFI Manipulation**
   - **Description**: Hacker compromised the GCR (@GCRClassic) X account to pump $ETHFI. An associated wallet (0x5e3) opened a $1M long on Hyperliquid just before the fake announcement, but closed at a small loss ($3.5K). This highlighted social engineering risks tied to trading.
   - **Losses**: Minimal direct ($3.5K for attacker), but indirect market volatility led to scattered liquidations.
   - **Impact**: Not a protocol exploit; user/social layer issue. Hyperliquid unaffected, but it underscored integration risks with off-chain signals.

#### 2. **March 2025: ETH Whale Liquidation Incident (0xf3f4 User)**
   - **Description**: A trader opened a massive $200M+ ETH long, built unrealized PNL, withdrew funds to lower margin, and timed a self-liquidation. This caught the HLP (Hyperliquid Liquidity Pool) off-guard, as it backstopped the trade at unfavorable prices. Hyperliquid denied it was a hack, calling it mechanics abuse. Max leverage reduced post-event (BTC to 40x, ETH to 25x).
   - **Losses**: Trader profited ~$1.8M; HLP lost ~$4M (1% of vault; all-time HLP PNL still +$60M). Over $80M in HLP withdrawals followed due to panic.
   - **Impact**: Exposed risks in large-position handling and withdrawal mechanics. Protocol patched leverage rules; community debated if it was an "exploit" or clever trading.

#### 3. **March 2025: JELLY/JELLYJELLY Price Manipulation Exploit**
   - **Description**: Attacker used three accounts for "self-liquidation" on JELLY perps: Deposited $7M quickly, opened $2.15M+ positions, manipulated prices via buy walls, then crashed it. This drained liquidity and forced HLP to cover. JELLY futures were paused.
   - **Losses**: Protocol/HLP ~$4M to $13.5M (reports vary; Arkham cited $4M direct, but cascading effects hit $13.5M). Attacker profited via manipulation.
   - **Impact**: Highlighted thin liquidity risks in altcoin perps. Hyperliquid paused trading; seen as a mechanics abuse rather than code bug. Community called for better oracle/liquidity safeguards.

#### 4. **July 2025: Cross-Chain Reference (GMX Reentrancy Exploit Mentioned in Context)**
   - **Description**: While not directly on Hyperliquid, discussions around perp DEX risks often referenced GMX's $42M reentrancy attack on its GLP pool. Hyperliquid users cited this as a cautionary tale for similar vaults like HLP.
   - **Losses**: N/A for Hyperliquid (GMX-specific: $42M).
   - **Impact**: Increased scrutiny on Hyperliquid's risk engine; no direct incident, but it fueled calls for audits.

#### 5. **September 2025: Major Trader Losses During Volatility**
   - **Description**: High-leverage trades imploded amid market swings, with one trader racking up $43M in losses. Not an exploit, but platform-wide volatility amplified rekts.
   - **Losses**: Individual: $43M; broader DeFi impact in the millions from cascading liquidations.
   - **Impact**: Showed leverage risks; Hyperliquid's speed enabled quick but devastating trades.

#### 6. **October 2025: Market Crash Mass Liquidations**
   - **Description**: Crypto wipeout led to historic liquidations. Four traders lost $10M+ each (e.g., 0x1a67: $18.73M wiped; 0x1d52: $16.43M, $140 left). Total event: Hundreds of millions across perps.
   - **Losses**: ~$100M+ in one day (Hyperliquid share significant); one user lost $21M from private key leak (not protocol fault).
   - **Impact**: Largest single-day rekts; highlighted CEX underreporting vs. on-chain transparency. Hyperliquid CEO noted CEXs hide similar events.

#### 7. **November 2025: POPCAT Price Manipulation Attack**
   - **Description**: Attacker deposited $3M USDC from OKX, split into 19 wallets, opened $20-30M longs, placed $30M buy wall at $0.21 to pump, then removed it causing 43% crash. Triggered mass shorts liquidation.
   - **Losses**: $63M total liquidations; HLP ~$4.9M. Attacker's wallets liquidated but profited indirectly.
   - **Impact**: Second major attack in 2025; exposed altcoin perp vulnerabilities. Trading paused briefly.

#### 8. **November 2025: General Price Manipulation (Second Incident)**
   - **Description**: Follow-up attack using similar tactics; details sparse but involved oracle/price feed abuse.
   - **Losses**: Over $1M (protocol and users).
   - **Impact**: Reinforced need for decentralized oracles; Hyperliquid iterated on HIP-3 for custom feeds.

#### 9. **January 2026: Ongoing Large Liquidations (e.g., BTC, HYPE, FARTCOIN)**
   - **Description**: Recent volatility: $5.1M BTC-USD single liquidation; $7.18M HYPE long capitulation; $1.45M FARTCOIN hit. Total 24h: $97M-$146M across 76K+ traders. Not exploits, but leverage-driven rekts.
   - **Losses**: Daily totals ~$100M+; individuals up to $7M+.
   - **Impact**: Standard for perps (86% trader loss rate per reports); Hyperliquid's 50% market share amplifies visibility.

| Event Date | Type | Key Asset | Estimated Losses | Cause |
|------------|------|-----------|------------------|-------|
| May 2024 | Manipulation | ETHFI | Minimal ($3.5K) | Social hack |
| March 2025 | Self-Liquidation | ETH | $4M (HLP) | Mechanics abuse |
| March 2025 | Exploit | JELLY | $4M-$13.5M | Price manipulation |
| Sept 2025 | Trader Loss | Various | $43M (individual) | Leverage volatility |
| Oct 2025 | Mass Liquidations | BTC/ETH | $100M+ | Market crash |
| Oct 2025 | User Hack | Various | $21M (individual) | Key compromise |
| Nov 2025 | Manipulation | POPCAT | $63M ($4.9M HLP) | Pump/dump |
| Nov 2025 | Manipulation | Unknown | $1M+ | Price attack |
| Jan 2026 | Liquidations | BTC/HYPE/FARTCOIN | $100M+ (daily) | Leverage |

Hyperliquid's team often attributes these to design (e.g., HLP as a risky vault) and has patched issues progressively. No major incidents reported in late 2025 beyond November. For real-time monitoring, check bots like @HyperRektBot or on-chain explorers.


### Statistical Trends in Hyperliquid Rekts

Based on the documented rekts from May 2024 to January 2026 (9 major events, excluding minor or referenced incidents), here's a statistical analysis of trends. These show an escalation in frequency and scale, particularly in 2025, as the platform grew in volume and attracted more sophisticated actors. Total documented losses: ~$341M.

- **Descriptive Stats on Losses**:
  - Mean loss per event: $37.86M
  - Median: $21M
  - Standard deviation: $41M (high variability, driven by mass liquidation outliers)
  - Min: $3.5K (minor manipulation)
  - Max: $100M (mass liquidations)

- **Trends Over Time**:
  - Events per year: 1 in 2024, 7 in 2025 (700% increase), 1 in early 2026.
  - Cumulative losses: Started low (~$3.5K by mid-2024), surged to ~$241M by end-2025, reaching $341M by Jan 2026.
  - Frequency trend: Isolated in 2024; clustered in 2025 (e.g., two in March, two in October, two in November), correlating with market volatility peaks and platform TVL growth (Hyperliquid's perp volume hit ~50% market share by late 2025).
  - Loss escalation: Early events averaged <$5M; later ones (2025-2026) averaged >$50M, with mass liquidations dominating post-September.

- **Breakdown by Type** (Loss Sums, Counts, Means, and % of Total):
  
| Type                  | Total Loss ($M) | Count | Mean Loss ($M) | % of Total Losses |
|-----------------------|-----------------|-------|----------------|-------------------|
| Mass Liquidations     | 200             | 2     | 100            | 58.7%            |
| Manipulation          | 64              | 3     | 21.3           | 18.8%            |
| Trader Loss           | 43              | 1     | 43             | 12.6%            |
| User Hack             | 21              | 1     | 21             | 6.2%             |
| Exploit/Manipulation  | 8.75            | 1     | 8.75           | 2.6%             |
| Self-Liquidation      | 4               | 1     | 4              | 1.2%             |

  - Key insight: Mass liquidations (e.g., October 2025 crash) drove the bulk (~59%), reflecting leverage risks in volatile markets. Manipulation rose sharply in 2025 (~21% of losses), often tied to altcoin perps with thin liquidity.

Recent X discussions confirm the trend continues into 2026, with daily liquidations averaging $100M+ (e.g., $5.1M BTC, $1.45M FARTCOIN on Jan 17-18), fueled by short squeezes on assets like BERA. Overall trend: As Hyperliquid scaled (e.g., 20k-200k TPS), rekts trended upward due to increased leverage usage and attacker sophistication, with manipulations peaking during altcoin hype.

### Why Transparent On-Chain Orderbooks Are a Good and Bad Idea for DEXes

Hyperliquid's fully on-chain orderbook (visible orders, positions, and trades) exemplifies this design. The rekts data fits here: Transparency exposed issues (e.g., verifiable liquidations), but also enabled attacks. Below, pros/cons based on DeFi analyses.

#### Good Ideas (Pros):
- **Trust and Verifiability**: All data is public and immutable on-chain, preventing CEX-like hidden manipulations (e.g., fake volume). In rekts, this allowed quick community detection of exploits (e.g., JELLY manipulation), fostering accountability.
- **Better Price Discovery and Depth**: Users see real-time market depth, enabling informed trading without relying on oracles alone. This reduced impermanent loss (common in AMMs) and attracted pro traders, contributing to Hyperliquid's 50% perp market share despite rekts.
- **Composability with DeFi**: Integrates seamlessly with other protocols (e.g., HyperEVM for custom contracts), allowing innovations like portfolio margining. In trends, this helped mitigate some losses via on-chain liquidations to the orderbook first.
- **Fit to Rekts Trend**: Transparency highlighted mass liquidations (~59% of losses), enabling protocol upgrades (e.g., leverage caps post-March 2025), which might have prevented worse opacity-driven disasters like FTX.

#### Bad Ideas (Cons):
- **Vulnerability to Front-Running and MEV**: Visible orders allow bots to sandwich trades or front-run, amplifying manipulations (~21% of losses, e.g., POPCAT pump/dump by spotting buy walls).
- **Exposes Positions to Attacks**: Public data invites targeted exploits (e.g., self-liquidation abuse in ETH whale case), especially in thin markets. Trends show this worsened in 2025 as visibility drew attackers.
- **Scalability and Cost Issues**: On-chain updates are gas-intensive and slow on some chains, leading to delays that exacerbate volatility-driven rekts (e.g., October crash).
- **Fit to Rekts Trend**: Transparency fueled ~80% of non-hack losses (manip + liqs), as attackers exploited visible liquidity gaps. In opaque CEXes, these might be hidden, but here they amplified user distrust during spikes.

Overall, for DEXes like Hyperliquid, transparency is a double-edged sword: It builds long-term trust but invites short-term exploits in high-leverage perps.

### Analysis of Major Motives Behind Rekts

From the data and broader DeFi exploits, motives cluster into profit-driven attacks and user/protocol risks. ~79% of Hyperliquid losses tied to deliberate motives (manip/exploit/self-liq), vs. accidental (trader losses/hacks).

- **Profit from Manipulation/Arb (Major: ~21% of losses)**: Attackers exploit thin liquidity or oracles to pump/dump (e.g., POPCAT: Deposit, build walls, crash for liquidation profits). Motive: Quick gains via volatility; common in perps due to leverage multipliers. Causes: Stale/vulnerable oracles, visible orders enabling prediction.
- **Mechanics Abuse for Gains (~4% but high impact)**: Self-liquidation (e.g., ETH whale: Withdraw to force bad prices on HLP). Motive: Exploit rules for risk-free profits; ties to transparent positions revealing exploitable gaps.
- **Leverage Gambling and Volatility (~71%: Mass liqs + trader losses)**: Users over-leverage, leading to wipes during crashes (e.g., Oct 2025: $100M+). Motive: Greed for high returns; amplified by market swings. Causes: No caps, user error.
- **Theft via Hacks (~6%)**: Key compromises or contract bugs (e.g., user hack). Motive: Direct theft; less common in Hyperliquid but seen in broader perps (reentrancy).

Trends show motives shifting: Early social hacks (2024) to sophisticated manip (2025), as transparency revealed more opportunities.

### Alternative Designs to Keep Orderbook and Position Data On-Chain

Hyperliquid's design (on-chain via HyperCore) is strong but vulnerable. Alternatives maintain full on-chain storage (for transparency) while adding security layers. Focus: Mitigate manip/mass liqs without off-chain reliance.

- **Fast L1 with Custom Consensus (e.g., Sei, Injective Models)**: Build on high-TPS chains like Sei (orderbook-optimized Cosmos L1) or Injective (CLOB on Cosmos). Keeps all data on-chain but uses parallel execution for sub-second speeds, reducing front-running windows. Fix: Lowers costs/delays that fueled Hyperliquid's volatility rekts. Hyperliquid's HyperBFT is similar—enhance with validator decentralization to prevent oracle manip.
- **Decentralized Oracles + Safeguards**: Integrate Chainlink/Pyth for medianized feeds (vs. validator-operated). Add TWAP (time-weighted averages) for prices and circuit breakers (pause trading on volatility spikes). Keeps orderbook/positions on-chain but verifies actions pre-execution, blocking manip like JELLY/POPCAT. Motive fix: Deters oracle exploits (~key manip cause).
- **MEV-Resistant On-Chain (with ZK Elements)**: Use private mempools or zk-SNARKs for order submission (encrypt until match, then reveal on-chain). Positions/orderbook remain fully on-chain post-execution for verifiability. Fix: Prevents front-running/sandwich while preserving transparency. Platforms like Econia (Aptos) experiment with this for DeFi composability.
- **Hybrid On-Chain Settlement with Audits**: Match orders on-chain via precompiles (like HyperEVM), but add rate limits on deposits/positions and dynamic leverage caps. All data stays on-chain; use multi-sig for upgrades. Motive fix: Curbs self-liq abuse and mass wipes. Real DEX prototypes hybrid low-latency while settling on-chain.

These designs could reduce Hyperliquid-style rekts by 50-70% (based on exploit patterns), keeping core on-chain benefits while addressing motives like manip. Implementation: Start with audits (e.g., Hacken's 19 pitfalls fixes) and progressive decentralization.