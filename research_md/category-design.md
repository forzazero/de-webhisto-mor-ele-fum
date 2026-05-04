PmOracle: A Domain-Specialized, Timeless Oracle for Decentralized Prediction Markets

**White Paper**  
**Version 1.0** – March 2026  
**Author Context**: Designed as an extension to existing pricefeed / rOracleData infrastructure for blockchain-based prediction markets.  
**Target Audience**: Blockchain developers, prediction market platform builders, oracle researchers, DeFi architects.

### Abstract

Prediction markets have grown rapidly in 2025–2026: combined platforms such as Polymarket and Kalshi reportedly process very large monthly notionals, with industry commentary pointing toward **$300B+** annualized run-rates for 2026 (figures vary by source and definition). Even with that growth, the **oracle problem** stays central — how to resolve real-world outcomes **on-chain** in ways that are reliable, hard to game, and operationally tractable.

PmOracle proposes a minimal, opinionated resolution framework built around **five recurring truth paradigms** rather than a shifting topic taxonomy. The paradigms — **AuthorityCertified**, **MarketPrice**, **TimestampedAnnouncement**, **ParametricMeasurement**, and **SubjectiveConsensus** — are meant to mirror distinct ways markets usually ground truth in practice (institutions, prices, timed announcements, measurements, and messy social judgment). In the authors’ intended design, that split supplies domain-specific **bias guards** for high-volume shapes of markets while keeping **SubjectiveConsensus** as an explicit home for long-tail and ambiguous questions.

The architecture sketches reuse familiar ingredients — caching, BLS aggregation, stake-weighted submissions — to aim for economic security and simplicity **without** repeatedly rewriting a monolithic resolver for each new headline category.

### 1. Introduction – The Prediction Market Oracle Challenge in 2026

Prediction markets aggregate beliefs into prices under incentive pressure; in many historical episodes they have tracked realized outcomes surprisingly well — though **when** and **why** they outperform simpler forecasts remains contested and context-dependent (election cycles, sports, macro shocks, and crypto narratives each behave differently).

Key platforms in early 2026:

- **Kalshi** (regulated, fiat): Sports dominates (~85–90% volume), with crypto, politics, and weather secondary.
- **Polymarket** (crypto-native): More balanced — Sports ~35–40%, Politics ~30–35%, Crypto ~15–30%, with geopolitics and culture growing.

Plausible industry trends for 2026–2027 (all contingent on regulation, product mix, and macro):

- Sports-heavy calendars (Olympics, leagues, UFC) continuing to anchor retail attention on some venues
- Crypto and macro-linked markets growing as rates, ETFs, and **RWA** narratives evolve
- Labor and macro prints attracting trading interest alongside mainstream coverage
- AI and “attention economy” questions spreading into long-tail markets
- Aggregate volume scenarios that extrapolate **~5×** 2025 — informative as orientation, not prophecy

Resolution remains the bottleneck:

- Manipulation vectors differ wildly (late sports bids, flash crashes, announcement timing, ambiguous memes).
- Generic oracles create huge attack surfaces or poor defaults.
- Topic categories age poorly (Crypto was "Novel" in 2025; may need split by 2027).

PmOracle’s bet is **truth-paradigm specialization**: bake resolution **logic families** into the core — each tuned to a different manipulation geometry — and ask creators to pick a paradigm at market creation instead of inventing a fresh resolver shape for every trending topic.

### 2. Core Philosophy – Truth Is Domain-Specific; Paradigms Are Eternal

**Working premise** (not a theorem): different **event classes** tend to differ along a few axes that matter for resolution:

- Which sources markets treat as authoritative
- How **finality** arrives in time — and how noisy fast signals are
- Where manipulation incentives concentrate — including favorite–longshot distortions
- What **defaults** should apply when wording or data are ambiguous

A **single** generic resolver can work for toy examples; at scale it often forces awkward compromises or dispute-heavy paths. Pure **topic** taxonomies, meanwhile, age poorly — yesterday’s “Novel” bucket becomes tomorrow’s mainstream category.

**Design response**: fix **five paradigm-level strategies** — stable resolution **shapes** — and map questions into them, rather than perpetually extending a topic list.


| Paradigm                | Truth Mechanism                           | Core Problem Solved                              | Key Guard / Bias Protection                       | 2026 Examples                                                | Future-Proof Examples (2030+)                                                             |
| ----------------------- | ----------------------------------------- | ------------------------------------------------ | ------------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| AuthorityCertified      | Official institutional certification      | Crowd vs. authority conflict                     | Official source priority + “No” on ambiguity      | Elections (FEC), USDA reports, Fed decisions, court verdicts | Regulatory approvals, Mars mission success, international treaty ratifications            |
| MarketPrice             | Aggregated / on-chain price feeds         | Volatility, flash crashes, conditional valuation | Median last-N + ignore <2¢ extreme moves          | BTC/ETH prices, ETF flows, Fed rate bands                    | Tokenized impact markets, futarchy policy metrics, AI compute rental rates                |
| TimestampedAnnouncement | Ranked public statements / media releases | Timing wars, source authenticity                 | Earliest valid timestamp from prioritized sources | Award shows, Elon announcements, AI model drops              | Autonomous agent milestone tweets, DAO governance executions, viral content confirmations |
| ParametricMeasurement   | Sensor / numeric physical data            | Precision, tampering, confidence bands           | Median + explicit bands                           | NOAA temps, production tons, hurricane strength              | Climate parametric insurance, earthquake magnitude, space radiation thresholds            |
| SubjectiveConsensus     | Meme / cultural / long-tail consensus     | “Nothing ever happens” + edge cases              | Strong default-to-“No” + high stake threshold     | Celebrity drama, Jesus return bets, memecoin virality        | Brain-computer interface adoption, alien contact claims, subjective AI safety thresholds  |


In this framework the five paradigms are intended to be **mutually exclusive choices at creation** and **jointly exhaustive** of the authors’ intended taxonomy: every market **should** pick exactly one primary paradigm. Real markets sometimes sit on boundaries; §7 discusses overlap and misfits explicitly — the claim is **engineering convenience and safety**, not logical necessity about “all possible questions forever.”

#### 2.1 Paradigm Details (from p1–p5)

**AuthorityCertified** — Resolution depends on an official, institutional, or authoritative certification (government body, regulatory agency, court, standards organization). It prioritizes finality from institutional sources over faster but noisier signals (media calls, exit polls). *Guards*: Official source priority; "No" bias on ambiguity; majority + authority tie-breaker; extended dispute/resolution windows. *Examples*:


| Event Type                        | Authority Source(s)                             | PmOracle Guard                                       |
| --------------------------------- | ----------------------------------------------- | ---------------------------------------------------- |
| Elections / Politics              | FEC, state boards, highest court                | "No" if no certification by cutoff; certified winner |
| Regulatory approvals              | FDA, SEC, CFTC, EU regulators                   | Defaults No on ambiguity or delay beyond deadline    |
| Official reports (GDP, recession) | USDA, BEA, Fed                                  | Strict numeric threshold from official PDF/release   |
| Court rulings                     | Supreme Court, appellate courts                 | Waits for mandate/issuance, not oral arguments       |
| Inauguration / succession         | Government gazette, congressional certification | Hierarchical source majority for competing claims    |


*When not to use*: Price data → MarketPrice; announcement timing → TimestampedAnnouncement; sensor data → ParametricMeasurement; meme/cultural → SubjectiveConsensus.

**MarketPrice** — Outcome determined by aggregated, verifiable market price or financial metric (on-chain feeds, decentralized oracles). Handles threshold/band bets, conditional valuation, and any market resolved by "did price hit X?" *Guards*: Median of last-N feeds; flash-crash guard (ignore <2¢ extreme moves); timestamp & deviation checks; stake-weighted aggregation; no front-running (committed post-event window). *Examples*: BTC/ETH price thresholds, Fed rate + crypto conditionals, ETF flow bets, tokenized RWA bands, futarchy policy metrics. *When not to use*: Institutional certification → AuthorityCertified; announcement timing → TimestampedAnnouncement; sensor/physical data → ParametricMeasurement; meme/virality → SubjectiveConsensus.

**TimestampedAnnouncement** — Outcome depends on a public statement, media release, or declaration; **when and from where** it occurs matters. Solves announcement timing wars (leaks, rumors, early tweets, competing sources). *Guards*: Ranked primary sources; earliest valid timestamp breaks ties; ignore invalid/manipulated entries; bias toward conservative outcome on conflicts; finality via stake-weighted aggregation. *Examples*: Award shows (Grammys, Oscars), product launches (GPT-7 release), CEO tweets/Spaces, celebrity announcements, viral moments. *When not to use*: Official institutional certification → AuthorityCertified; price feeds → MarketPrice; numeric sensor data → ParametricMeasurement; broad meme/crowd wisdom → SubjectiveConsensus.

**ParametricMeasurement** — Outcome depends on objective, numeric, physical/sensor-derived measurements (instruments, satellites, meteorological stations, production counters). *Guards*: Primary source priority; median aggregation; explicit confidence bands / error tolerance; provisional vs. final read preference; threshold strictness; fallback default to conservative outcome; ignore micro-manipulation outliers. *Examples*:


| Market Example                        | Resolution Source               | Guard Applied                                |
| ------------------------------------- | ------------------------------- | -------------------------------------------- |
| LA daily max temp >75°F               | NWS daily report                | Median + confidence band (±1°F)              |
| NYC rainfall >2 in/week               | NOAA or gauge network           | Final accumulated value priority             |
| US corn production >15B bushels       | USDA Final Estimate             | Exact numeric threshold parse                |
| Land-Ocean temp index >1.28°C anomaly | NASA GISS annual release        | Final revised value + "No" bias on ambiguity |
| Temp in 70–74°F bin                   | Official meteorological service | Range-based median matching                  |


*When not to use*: Institutional certification → AuthorityCertified; price feeds → MarketPrice; announcement timing → TimestampedAnnouncement; subjective/meme → SubjectiveConsensus.

**SubjectiveConsensus** — Long-tail, ambiguous, meme-driven, cultural, or interpretive events where truth depends on broad human judgment or social consensus. Serves as the **eternal escape hatch** for everything outside the other four. *Guards*: Strong default bias to "No" (counters pro-Yes bias); high stake-weight threshold for Yes; stake-weighted aggregation with slashing; ambiguity fallback to No; no single-source reliance. *Event types*: Memecoin virality ("Will X reach $1B mcap?"); extreme long-shot prophecies ("Jesus returns", "Aliens confirmed"); celebrity drama/gossip; vague geopolitical thresholds ("civil war" definition); futarchy/DAO decisions (interpretive execution); attention/hype without clear metrics ("Will AI model X be called smartest?"). Usually <10% of notional volume but absolute $ grows. *When not to use*: Any event with a clear authority, price, announcement, or measurement source → use the matching paradigm.

**Why exactly five?** Fewer buckets tend to dump difficult cases into **SubjectiveConsensus**, weakening guards where measurable structure exists. More buckets multiply maintenance and cross-paradigm disputes unless each extra bucket clears a high bar for **distinct manipulation physics**.

The authors argue these five mirror recurring resolution **modes** — institutional certification, priced observables, timed public statements, numeric physical readings, and coarse social judgment — not that history will never surface a sixth mode worth a hard fork (§7 leaves room for that). New headlines still map to old epistemologies: an “AI milestone” might be an **announcement**, a **benchmark**, or a fuzzy cultural judgment depending on how the question is written.

The broader lesson — specialize where **how truth is established** differs — is independent of whether five is the uniquely optimal count.

For full paradigm specifications, real-world example tables, and when-to-use guidance, see `p1.md` (AuthorityCertified), `p2.md` (MarketPrice), `p3.md` (TimestampedAnnouncement), `p4.md` (ParametricMeasurement), and `p5.md` (SubjectiveConsensus).

**Mapping from legacy topic categories.** The original six topic categories (Election, Sports, Entertainment, Weather, Production, Novel) map cleanly: Election → AuthorityCertified; Sports/Entertainment → TimestampedAnnouncement (or MarketPrice for odds); Weather/Production → ParametricMeasurement; Novel → SubjectiveConsensus. Crypto, AI-Tech, and futarchy markets fit natively into MarketPrice and SubjectiveConsensus without forcing them into Novel.

#### 2.2 Paradigm-to-Market Selection

When creating a market, ask: *"What is the fundamental way truth gets established for this question?"* Then select the matching paradigm.


| Paradigm                    | Selection Criterion                                       | 2026 Examples                                                       |
| --------------------------- | --------------------------------------------------------- | ------------------------------------------------------------------- |
| **AuthorityCertified**      | Truth from official institution (govt, court, regulator)? | Elections (FEC), BLS unemployment, SEC approvals, court rulings     |
| **MarketPrice**             | Truth from aggregated price/financial data?               | BTC/ETH thresholds, ETF flows, Fed rate conditionals                |
| **TimestampedAnnouncement** | Truth from public announcement where timing matters?      | Award shows, GPT-7 release, CEO tweets, hiring freeze announcements |
| **ParametricMeasurement**   | Truth from numeric sensor/official measurement?           | NOAA temps, USDA production, BLS wage bands, hurricane strength     |
| **SubjectiveConsensus**     | Meme, cultural, interpretive, or long-tail?               | AI displacement, 4-day workweek, memecoin virality                  |


For concrete 2026 market examples per paradigm (Polymarket, Kalshi), see `paradigm-market-mappings.md`.

#### 2.3 When-Not-to-Use Quick Reference

Avoid wrong paradigm selection by checking the primary truth source:


| If truth comes from…                          | Use                     | Do *not* use                                                                            |
| --------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------------- |
| Official institution (govt, court, regulator) | AuthorityCertified      | MarketPrice, TimestampedAnnouncement, ParametricMeasurement, SubjectiveConsensus        |
| Aggregated price / financial feed             | MarketPrice             | AuthorityCertified, TimestampedAnnouncement, ParametricMeasurement, SubjectiveConsensus |
| Public announcement (timing matters)          | TimestampedAnnouncement | AuthorityCertified, MarketPrice, ParametricMeasurement, SubjectiveConsensus             |
| Numeric sensor / official measurement         | ParametricMeasurement   | AuthorityCertified, MarketPrice, TimestampedAnnouncement, SubjectiveConsensus           |
| Meme, cultural, interpretive, long-tail       | SubjectiveConsensus     | AuthorityCertified, MarketPrice, TimestampedAnnouncement, ParametricMeasurement         |


For per-paradigm guards, truth problems solved, and real-world examples, see `p1.md`–`p5.md`.

### 3. Architecture & Design

PmOracle reuses existing infrastructure for economic security. All paradigms share the same conceptual pipeline:

- Sharded cache for hot and cold reads
- BLS threshold signatures for on-chain publication
- Stake-weighted aggregation of agent submissions
- Slashing for malicious proposals

**Core design:** The system recognizes exactly five resolution paradigms. Market creation specifies paradigm plus minimal parameters (e.g., source URLs, confidence bands, threshold values).

Each paradigm encodes guards motivated by historical failure modes — for example, late-window manipulation patterns in sports-like markets (timing-aware filters), and conservative **No** defaults where outcomes depend on diffuse social interpretation.

Resolution flow:

1. Market creator selects paradigm at launch.
2. Agents submit observations aligned to paradigm rules.
3. Stake-weighted aggregation + paradigm-specific logic.
4. BLS multisig publishes resolved outcome on-chain.
5. Contracts retrieve the resolved outcome via a simple hot-path call.

No core changes are ever needed for new topics—only market-side selection. SubjectiveConsensus is the eternal escape hatch: novel markets (memes, crypto events, AI milestones, futarchy) pick it at creation and receive the appropriate bias guards. New market types are added by selecting the paradigm, not by modifying the core engine.

#### 3.1 Agent Submission Pipeline

Agents (trusted monitors or permissionless) submit observations to the router. **Trusted monitors** (NOAA, USDA, official APIs) are whitelisted and auto-boosted. **Permissionless agents** sign payloads and receive lower default weight. The system validates market existence, payload structure, freshness, signature, rate limits, and paradigm consistency. Valid submissions feed the cache; the coordinator applies paradigm-specific guards at resolution.

#### 3.2 Resolution & Settlement Cycle

**End-to-end lifecycle:** Market creation → data submission → aggregation → resolution (paradigm-specific) → on-chain publication → settlement. Paradigm-specific decision points: AuthorityCertified waits for official release; MarketPrice uses median last-N; TimestampedAnnouncement uses earliest valid timestamp; ParametricMeasurement uses median + bands; SubjectiveConsensus applies default-to-No + stake threshold.

**Resolution:** At deadline, the coordinator enters a resolving state. It pulls aggregated data from the cache, selects the paradigm strategy, applies guards and resolution criteria, and produces a resolved outcome. If confidence falls below threshold, it retries (up to five attempts). The outcome is stored; BLS multisig publishes on-chain. Status becomes Resolved (or Disputed if a challenge is pending).

**Settlement:** Prediction-market contracts request settlement; the oracle returns the outcome in under 20 µs. Winning shares pay $1 equivalent; losing shares pay $0. Payouts credit instantly (gasless). Binary YES/NO is the default resolution type; multi-outcome and YES-only are supported for specific use cases.

#### 3.3 Market Creation Flow

The creator submits a market-creation request with paradigm, market identifier, and optional resolution rules (source URLs, thresholds). The system normalizes input, validates format and schema, checks uniqueness, derives bias-guard defaults from the paradigm, and stores the market in the registry. Status becomes Active immediately. Permissionless—no approval gate.

#### 3.4 Migration from Topic-Based Design

The paradigm swap is framed as a **one-time migration** off topic enums — ongoing work shifts toward **parameter** tuning rather than adding categories ad hoc. Legacy topic categories map cleanly: Election and Production → AuthorityCertified; Sports and Entertainment → TimestampedAnnouncement; Weather and Production → ParametricMeasurement; Novel → SubjectiveConsensus. MarketPrice is added as a first-class paradigm for price-based resolution. Existing markets using legacy categories can be mapped at read time; new markets always use paradigm selection.

### 4. Security & Economic Design

- **Bias guards** per paradigm target recurring manipulation shapes (favorite–longshot skew, late-window bids, ambiguity farming). Details live in §2.1; at a high level: **AuthorityCertified** prioritizes certified institutional sources; **MarketPrice** uses robust aggregates plus flash-move filters; **TimestampedAnnouncement** privileges well-ranked sources and earliest valid timestamps; **ParametricMeasurement** uses medians and explicit tolerance bands; **SubjectiveConsensus** leans conservative (**No**) when interpretation is unclear.
- **Stake-weighting** + slashing disincentivize attacks.
- **Default-to-No** in ambiguous SubjectiveConsensus cases protects against “nothing ever happens” pump-and-dump.
- **Extensibility** with minimal taxonomy churn: novel question shapes default into **SubjectiveConsensus** rather than forcing a new topic enum per hype cycle.

Compared to rough 2026 archetypes (each simplified):

- **Kalshi**: centralized rulebooks and curated sources per vertical — strong operational clarity, different trust model than fully on-chain stacks.
- **Polymarket / UMA-style paths**: optimistic dispute games — flexible, but ambiguity can route disproportionate volume into challenges.
- **PmOracle (as proposed)**: paradigm-specialized defaults up front — plausibly fewer disputes on “bread and butter” shapes of markets; tradeoffs include rigidity at paradigm boundaries and governance over guard parameters.

#### 4.2 Security Model (Per-Paradigm)

**Slashing rules per paradigm:**

| Paradigm | Slashing Trigger | Bond / Stake | Dispute Window |
|----------|------------------|--------------|----------------|
| AuthorityCertified | Submitting non-official source as authoritative; contradicting certified release | 5–10% of open interest | 24–48 h post-resolution |
| MarketPrice | Submitting extreme outlier (>3σ) after event; front-running feed | Stake-weighted; outlier slashed | 24 h (short; price finality) |
| TimestampedAnnouncement | Fabricated timestamp; source spoofing; late-bid manipulation | 5–10% of open interest | 48–72 h |
| ParametricMeasurement | Tampering with sensor data; submitting outside confidence band without provenance | Stake-weighted; outlier slashed | 24–48 h |
| SubjectiveConsensus | Coordinated Yes-pump; nothing-ever-happens exploit | High stake threshold; slashing for outliers | 72 h (longer; interpretive) |

**Attack vectors & mitigations:**

| Vector | Paradigm(s) at Risk | Mitigation |
|--------|---------------------|------------|
| Late-bid manipulation | TimestampedAnnouncement, MarketPrice | Yogi-Berra guard: ignore &lt;2¢ bids in final 4 h; committed post-event window |
| Flash-crash | MarketPrice | Median last-N; ignore &lt;2¢ extreme moves; deviation checks |
| Source capture | AuthorityCertified | Official source priority; "No" on ambiguity; no single-source reliance |
| Ambiguity exploit | SubjectiveConsensus | Strong default-to-No; high stake threshold for Yes |
| Nothing-ever-happens | SubjectiveConsensus | Default-to-No; slashing for coordinated Yes-pump |

**Economic model:** Proposers/resolvers stake; honest high-rep agents dominate aggregation. Slashed bonds split: 50% to correct-side holders, 50% to treasury (or operator). Fee structure: ~0.1–0.5% settlement fee; optional dispute bond cut. Capital efficiency: stake-weighted aggregation; no over-collateralization for typical markets.

**SubjectiveConsensus edge case:** At scale, "nothing ever happens" can manifest as long dispute windows or low-confidence stalemates. Mitigation: hard cap on resolution attempts (e.g., five); after cap, default-to-No; governance can tune the stake-threshold parameter to tighten or loosen.

### 5. Comparison: Topic-Based vs. Paradigm-Based


| Dimension               | Topic-Based (e.g. original 6 categories) | Paradigm-Based (PmOracle v1)      |
| ----------------------- | ---------------------------------------- | --------------------------------- |
| Long-term stability     | Requires periodic taxonomy reviews       | Paradigm set fixed by design intent |
| Coverage                | ~95% today, leaks to Novel               | Aims for systematic coverage via paradigms |
| Manipulation resistance | Good but topic-tied                      | Domain-tuned guards per paradigm        |
| Futarchy / Impact Mkts  | Forced into Novel                        | Native (MarketPrice + Subjective) |
| Maintenance burden      | Periodic taxonomy updates               | One-time swap, five paradigms     |


**Historical context — the original six topic categories.** PmOracle’s predecessor design used six topic-based categories: Election, Sports, Entertainment, Weather, Production, and Novel. They were chosen to cover a large share of observed prediction-market volume (the paper cites ~95% from 2025–2026 platform mixes — an estimate, not a physical constant) while staying maintainable. Fewer categories tended to overload **Novel**; more categories inflated registry work. Six was a pragmatic compromise. The paradigm-based step tries to keep that pragmatism while anchoring categories to **how** truth is verified rather than **what** is trendy.

**Philosophy in one sentence.** *Bias guards and resolution defaults toward “obvious” high-volume shapes of markets, route genuine ambiguity into an explicit conservative lane, and avoid perpetual core rewrites for new topics.* The intent is opinionated where mistakes are expensive and comparatively hands-off on creative long tails — at the cost of careful upfront question-writing and occasional paradigm-sort judgment calls.

#### 5.2 Performance & Cost Analysis

| Metric | Target | Notes |
|--------|--------|-------|
| Hotpath read | &lt;20 µs | Resolved outcome from cache or on-chain; no health/validation gate |
| Gas per resolution | ~80k | Single BLS-threshold publish via MorpheumOraclePublisher |
| Resolution latency | &lt;30 s | Coordinator runs every 30 s; resolution at deadline − 5 min |
| Throughput | 10k+ markets/day | Sharded cache; coordinator batch processing; no bottleneck on strategy selection |
| Storage impact | Minimal | Compact resolved-outcome format; registry stores paradigm and criteria; no per-feed bloat |

Settlement is gasless for users (read-only); resolution cost amortized across platform.

### 6. Volume & Platform Context (2026)

Platform volume breakdowns (early 2026) validate paradigm coverage:


| Platform       | Dominant categories (approx. share)                                               | Notes                                                                                                                                        |
| -------------- | --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **Kalshi**     | Sports ≈ 85–90%; Crypto + Entertainment + Politics (remaining)                    | Super Bowl drove massive spikes; sports is the main retail entry point. Politics/economics secondary.                                        |
| **Polymarket** | Sports ≈ 35–40%; Politics ≈ 30–35%; Crypto ≈ 15–30%; Other (geopolitics, culture) | More balanced; geopolitics and niche events (e.g. cabinet exits, cultural bets) pull significant attention. Weekly volumes frequently > $1B. |
| **Combined**   | Sports + Politics + Crypto ≈ 90%+ of notional volume                              | Industry run-rate heading toward $300B+ annualized in 2026 (from ~$60B in 2025).                                                             |


**Expected 2026–2027 shifts.**


| Paradigm / Topic   | 2025–Early 2026 Share                   | Expected 2026–2027 Trend                            | PmOracle Action                                                           |
| ------------------ | --------------------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------- |
| Sports             | Very high (Kalshi) / Medium-high (Poly) | Strongest growth; could dominate non-election years | Strengthen TimestampedAnnouncement / MarketPrice guards                   |
| Politics           | High                                    | Cyclical peaks (midterms 2026)                      | AuthorityCertified as-is; add seasonal tuning                             |
| Crypto/Econ        | Medium-high                             | Fastest relative gainer                             | MarketPrice; monitor for flash-crash guards                               |
| Entertainment      | Medium-low                              | Stable                                              | TimestampedAnnouncement fine as-is                                        |
| Weather            | Low-medium                              | Steady growth (parametric)                          | ParametricMeasurement reinforced                                          |
| Production         | Low                                     | Steady/niche                                        | ParametricMeasurement fine                                                |
| Novel / Long-tail  | Low but diverse                         | Growing absolute $, shrinking %                     | SubjectiveConsensus escape hatch                                          |
| AI/Tech            | Very low → emerging                     | High growth potential                               | Watch; fits SubjectiveConsensus or TimestampedAnnouncement                |
| Job market / Labor | Medium-high (growing)                   | Fastest-growing macro domain; Fed/Wall St. watches  | AuthorityCertified (BLS) dominates; SubjectiveConsensus for AI-labor tail |


#### 6.1 Job Market Economic Activities

Job market prediction markets are one of the fastest-growing and most economically impactful use cases in early 2026. They let participants trade on real outcomes: unemployment rates, jobs added, wage growth, AI-driven displacement, hiring freezes, etc. These markets generate millions in volume on Polymarket and Kalshi because they directly influence trading in stocks, bonds, crypto, and corporate planning. Fed researchers and Wall Street now watch them because they often beat traditional economist surveys.

**Paradigm mapping for job-market use cases:**


| Paradigm                    | % of Job-Market Volume (2026 est.) | Typical Questions                                   | Resolution Source / Guard                                                         | Real 2026 Examples                                                                                                                           |
| --------------------------- | ---------------------------------- | --------------------------------------------------- | --------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **AuthorityCertified**      | ~80–90%                            | Official government statistics, certified releases  | BLS Employment Situation Report priority + "No" bias on revisions/ambiguity       | "February 2026 Unemployment Rate"; "How many jobs added?"; "Will unemployment reach ≥5.0% in 2026?"                                          |
| **TimestampedAnnouncement** | ~5–10%                             | Company announcements, CEO statements, policy drops | Earliest valid timestamp (press release > earnings transcript > executive X post) | "Will Google/Meta announce hiring freeze in Q2 earnings?"; "Will Elon Musk announce mass Tesla/xAI hires by June 30?"                        |
| **ParametricMeasurement**   | ~5%                                | Precise numeric thresholds, bands                   | Median of feeds + confidence bands                                                | "Unemployment in 4.3–4.5% band for March 2026"; "Average hourly wage growth >3.5% YoY"                                                       |
| **MarketPrice**             | <5% (emerging)                     | On-chain/financial labor signals                    | Median last-N feeds + flash-crash guard                                           | Tokenized wage-index futures; "Blockchain dev salary median >$200k" via on-chain job boards                                                  |
| **SubjectiveConsensus**     | <5% now, fastest-growing tail      | Interpretive, AI-impact, cultural questions         | Strong default-to-"No" + high stake threshold                                     | "Will AI displace more jobs than it creates by 2030?"; "Will 4-day workweek become standard in tech by 2028?"; "Will remote work stay >30%?" |


**Why job market fits PmOracle:** Much macro–labor volume naturally sits under **AuthorityCertified** (e.g., BLS-style releases) — official prints as primary truth, with careful rules around revisions and ambiguity. That pattern can **reduce** classes of dispute common in looser oracle designs; it does not remove politics around definitions or release timing. Interpretive tail questions (AI displacement narratives, cultural shifts about work) fit **SubjectiveConsensus** with conservative defaults. Rather than minting “Jobs” as an eternal topic pillar, the mapping argues those questions still fall under one of the five paradigms — though wording quality matters enormously.

**Economic benefits:** Companies can issue prediction-market bonds tied to hiring numbers; workers can hedge job-loss risk. DeFi loans can auto-liquidate on unemployment oracles. Futarchy/policy markets ("Should minimum wage rise?") resolve via AuthorityCertified + SubjectiveConsensus. For full job-market paradigm mapping and the "paradigm sort" application flow, see `job-market.md`.

#### 6.2 Agent–Human Labor Market (Emerging 2026)

The **agent world** is rapidly evolving into something that looks like a **labor/skills/job market**, but with fundamental structural differences from the human one. Prediction markets for agent skills—and for human–agent displacement—map cleanly onto PmOracle’s paradigm framework and extend the job-market use case into the fastest-growing edge of the labor domain.

**Core analogy: Agent skills marketplace ≈ Human job market (with radical differences)**


| Dimension              | Human Labor / Job Market (2025–2026)      | AI Agent Skills / Capability Market (emerging 2026)                                  | Implication for PmOracle                                                                         |
| ---------------------- | ----------------------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| **Supply side**        | Finite humans, bounded by biology         | Infinite replication, near-zero marginal cost after training                         | Supply elastic → prices crash on commoditized skills; premium on unique/fine-tuned agents        |
| **Availability**       | Max ~16 productive hours/day              | True 24/7/365, instant scaling                                                       | Agents win on reliability → markets reward uptime + SLA guarantees                               |
| **Hiring / Matching**  | Slow (weeks/months), resumes + interviews | Instant (seconds), programmatic via embeddings/proofs/benchmarks                     | Reputation + verifiable proofs become the resume; intent layer can auto-negotiate                |
| **Pricing model**      | Mostly salary + benefits (sticky, annual) | Pay-per-task, pay-per-result, subscription, token buyback-burn                       | Dynamic, outcome-based pricing → prediction markets forecast agent performance & set fair prices |
| **Skill evolution**    | Slow (years of experience)                | Fast (fine-tune in hours, merge LoRAs)                                               | Skills depreciate rapidly → constant re-validation & reputation updates                          |
| **Reputation / Trust** | References, LinkedIn, track record        | On-chain proofs (backtests, TEE attestations, eval scores), stake-weighted, slashing | Identity + VC + validation modules = programmatic trust                                          |
| **Labor substitution** | Humans displaced slowly by automation     | Agents displace agents (or humans) very quickly on price/performance                 | Winner-takes-most → top 1% agents capture most value                                             |


**Paradigm mapping for agent–human labor markets**

Agent-skill and displacement questions slot into existing paradigms:


| Paradigm                    | Agent-Labor Use Cases                                                                                                                            | Resolution Source / Guard                                                                        |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| **AuthorityCertified**      | Official labor stats on AI adoption; certified regulatory releases                                                                               | BLS / OECD / EU reports on automation; "No" bias on ambiguity                                    |
| **TimestampedAnnouncement** | Company announcements (hiring freezes, agent rollouts, CEO statements)                                                                           | Earliest valid timestamp from press release > earnings > executive post                          |
| **ParametricMeasurement**   | Numeric thresholds (SWE-bench score, Sharpe ratio, uptime %)                                                                                     | Median of feeds + confidence bands; explicit benchmarks                                          |
| **MarketPrice**             | On-chain agent earnings, tokenized labor signals                                                                                                 | Median last-N feeds + flash-crash guard                                                          |
| **SubjectiveConsensus**     | "Will AI displace more jobs than it creates by 2030?"; "Will 4-day workweek become standard?"; "Which agent will dominate SWE-bench by Q4 2026?" | Strong default-to-"No" + high stake threshold; fits interpretive, cultural, trajectory questions |


**2026 reality check**

Early platforms already embody this:

- **Moltlaunch** (Feb 2026): gig platform where users "hire" AI agents; agents have tradable tokens; earnings buy back & burn → direct financialization of agent productivity
- **AGI Jobs v0**: decentralized marketplace for "AGI labor" — post tasks, agents bid/execute
- **Enso, Agentforce (Salesforce), MultiOn**: micro-agents for specific skills (LinkedIn writer, SEO specialist) sold as $49/mo bundles
- **DX Terminal Pro**: $6M+ handed to autonomous trading agents in experiments

**Why this matters for PmOracle**

- Most agent-labor questions are **SubjectiveConsensus** (trajectory, displacement, cultural shifts) or **ParametricMeasurement** (benchmark scores, economic outcomes).
- No new paradigm is needed—agent skills and human–agent displacement map onto the same five paradigms.
- Prediction markets on agent performance create a meta-layer: participants bet on agent skill trajectories → informs reputation weighting, capital allocation, and investment.

For full analysis—module-by-module Mormcore extension, resolution strategies for agent categories, and the virtuous cycle from commoditized supply to trusted scarcity—see `agent-human-market.md`.

#### 6.3 Agent Capability Data Model

The **Agent Capability** model is the on-chain "resume" for AI agents—replacing LinkedIn profiles with verifiable, slashable, stake-backed credentials. It defines what an agent can do, how it proves it, and how it is discovered.

**Agent capability record:** The on-chain "resume" for AI agents captures: agent identity, skill category (Trading, Coding, Content, Research, Ops, Novel), skills representation, proofs (benchmarks, attestations, PnL), SLA claims (uptime, latency, success rate), pricing model (pay-per-task, pay-per-result, subscription, token buyback-burn), version, and reputation score.

**Proof types:** Benchmark (SWE-bench, Sharpe, conversion rate), TEE attestation, PnL (on-chain/audited), EvalArena (LMSYS-style), HumanJudge (SubjectiveConsensus-style).

**Discovery:** Buyers query by skill category, required proofs, minimum reputation, and SLA claims. The intent layer embeds the buyer request and ranks agents by reputation, proof match, and semantic similarity. For full design, see `agent-capability-model.md`.

#### 6.4 Agent Skill Resolution Strategies

Agent skill categories map to PmOracle paradigms by outcome type. **Trading agents** (quant, arbitrage, market-making) use **ParametricMeasurement** (Sharpe, PnL, drawdown) or **MarketPrice** (on-chain balance); never SubjectiveConsensus for core performance.


| Skill Category | Primary Paradigm                             | Key Proofs                                             |
| -------------- | -------------------------------------------- | ------------------------------------------------------ |
| **Trading**    | ParametricMeasurement, MarketPrice           | Sharpe ratio, max drawdown, on-chain PnL               |
| **Coding**     | ParametricMeasurement                        | SWE-bench, HumanEval, eval arena scores                |
| **Content**    | ParametricMeasurement or SubjectiveConsensus | Conversion rate (parametric); "best copy" (subjective) |
| **Research**   | ParametricMeasurement                        | Citation count, accuracy on fact-check tasks           |
| **Ops**        | ParametricMeasurement                        | SLA metrics (uptime, latency, success rate)            |
| **Novel**      | SubjectiveConsensus                          | Interpretive; default-to-No                            |


**Trading Agent resolution flow:** Market creator specifies ParametricMeasurement paradigm with threshold (e.g., Sharpe ≥ 2.0), source, and confidence band. Agents submit observations; the strategy resolves via median plus bands; BLS publishes. Guards: primary source priority, median aggregation, explicit bands, default-to-conservative on ambiguity. For full Trading Agent strategy and extension to other categories, see `agent-skill-resolution.md`.

#### 6.5 End-to-End Agent Hire Flow

**Scenario:** Buyer needs a marketing agent for a LinkedIn campaign. Target: **under 10 seconds** for matching; full flow in minutes to hours.

1. **Intent post** — Buyer: "LinkedIn campaign, conversion >2%, max $500." System extracts filters: content category, conversion threshold, budget.
2. **Discovery** — Directory: semantic search, rank by reputation × proof_match × semantic_similarity; return top 5.
3. **Matching** — Intent proposes ranked list; buyer selects agent (or auto-negotiates).
4. **Escrow** — Marketplace + Bank: lock funds; task spec attached; agent accepts.
5. **Execution** — Agent runs campaign; delivers metrics; submits proof (e.g., conversion_rate = 2.4%).
6. **Evaluation** — ParametricMeasurement (platform API) or buyer sign-off; PmOracle resolves if disputed.
7. **Settlement** — Bank releases escrow; Reputation updates score; proof stored in memory for future discovery.

**Prediction market overlay:** "Will Agent A achieve >2% conversion?" → ParametricMeasurement. For full flow diagram, module mapping, and edge cases, see `agent-hire-flow.md`.

#### 6.6 Paradigm Sort: Application Flow

The "paradigm sort" skill assigns every market question to exactly one paradigm at creation. **Flow:**

1. **Ask**: "What is the fundamental way truth gets established for this question?"
2. **Map**: Official institution → AuthorityCertified; price/financial → MarketPrice; announcement timing → TimestampedAnnouncement; numeric/sensor → ParametricMeasurement; interpretive/meme → SubjectiveConsensus.
3. **Select** paradigm at creation → system auto-applies the correct resolution strategy with its guards.

No rewrites needed later—even if AI-labor questions surge to 20–30% volume by 2027–2028, they stay in SubjectiveConsensus or shift to AuthorityCertified if governments start certifying impact reports. For job-market-specific examples and the full sorted mapping, see `job-market.md`.

#### 6.7 Agent Economy Deep Dive

The agent skills marketplace is the fastest-growing extension of the job-market domain. PmOracle provides resolution for prediction markets on agent performance; Mormcore modules (directory, memory, reputation, marketplace, intent) handle matching and settlement.

**Agent capability record** (see `agent-capability-model.md`): agent identity, skill category (Trading, Coding, Content, Research, Ops, Novel), skills representation, proofs (benchmarks, TEE attestations, PnL), SLA claims (uptime, latency, success rate), pricing model, version, reputation score.

**End-to-end hire flow** (target &lt;10 s matching): (1) Intent post → (2) Directory semantic search → (3) Rank by reputation, proof match, and semantic similarity → (4) Escrow (bank) → (5) Execution → (6) Evaluation (ParametricMeasurement or buyer sign-off) → (7) Settlement → (8) Reputation update.

**Paradigm resolution for agent-skill markets:**

| Agent Question Type | Paradigm | Example | Resolution Source |
|---------------------|----------|---------|-------------------|
| Benchmark threshold | ParametricMeasurement | "Will agent X achieve Sharpe ≥ 2.0?" | Median of feeds + confidence bands; backtest URI |
| On-chain PnL | MarketPrice | "Will agent X's on-chain PnL exceed $1M?" | Median last-N feeds + flash-crash guard |
| Trajectory / displacement | SubjectiveConsensus | "Will AI displace more jobs than it creates?" | Default-to-No + high stake threshold |
| Company announcement | TimestampedAnnouncement | "Will Google announce agent hiring freeze?" | Earliest valid timestamp |

**Trading agent resolution (Sharpe-ratio proof):** Market creator specifies ParametricMeasurement paradigm with threshold (e.g., Sharpe ≥ 2.0), source, and confidence band. Agents submit observations; strategy resolves via median plus bands; BLS publishes. No new paradigm—agent skills map to existing five.

For full schema, module flow, hire-flow diagram, and edge cases, see `agent-capability-model.md`, `agent-hire-flow.md`, `agent-skill-resolution.md`.

Sports-heavy calendars, election cycles, crypto–macro narratives, and emerging AI–attention markets each pull topic mix in different directions; parametric weather and insurance-linked markets may grow on their own slope. Long-tail **Novel** questions can rise in dollar terms even as share falls — which keeps a conservative **SubjectiveConsensus** default economically relevant. The paradigm framing is meant to absorb those shifts via guard tuning more often than via taxonomy reinvention; whether that holds in practice is an empirical question.

**Source attribution.** Volume and share figures come from analytics trackers (Artemis, Dune, Defirate/Allium, Messari), platform announcements, and media coverage (Gambling Insider, FalconX, Financial Times) as of early March 2026. Key supporting stats: Kalshi sports 76–90% of volume (Gambling Insider, FT); Polymarket Feb 2026 Sports ~34.5%, Politics ~32.6%, Crypto ~29.6% (Defirate); industry YTD 2026 on track for >$325B (FalconX, 5× 2025); combined Kalshi + Polymarket Feb ~$17.9–23.4B. Cross-verified via Artemis, Dune on-chain, Allium feeds.

### 7. Risks, Limitations & Governance

**Risks & limitations:**

| Risk | Description | Mitigation |
|------|-------------|------------|
| Paradigm overlap | Some markets could fit multiple paradigms (e.g., BLS release + price derivative) | Creator selects primary truth source at creation; paradigm sort flow (§6.6) guides choice |
| Guard tuning | Flash-crash window, stake thresholds may need adjustment as markets evolve | Governance-controlled parameters; on-chain proposal + stake-weighted voting |
| New truth mechanism (2030+) | A sixth way to verify events could emerge (e.g., AI evaluator consensus) | SubjectiveConsensus is escape hatch; governance can add paradigm via hard fork if proven necessary |
| Nothing-ever-happens at scale | SubjectiveConsensus markets may stall on low confidence | Hard cap on resolution attempts; default-to-No; governance tunes threshold |
| Source capture | Single authoritative source could be compromised | No single-source reliance; ranked sources; dispute window |

**Governance & evolution:** Guard parameters (e.g., flash-crash window, confidence bands, stake thresholds) are tunable via on-chain governance. Proposals require stake-weighted approval; no taxonomy change needed for tuning—only parameter updates. If a new truth mechanism emerges, community can propose a sixth paradigm; until then, SubjectiveConsensus covers novelty.

**Long-term adaptability:** 2027 monitoring: if Crypto or AI-Tech consistently &gt;10–15% volume, tune MarketPrice / TimestampedAnnouncement guards—no taxonomy change. AI-assisted submission agents can enhance accuracy without altering paradigms.

### 8. Roadmap & Evolution

- 2026: Target launch posture with five paradigms aligned to present volume mixes (subject to shipping constraints).
- 2027 monitoring: If Crypto or AI-tech verticals stabilize above rough **10–15%** share thresholds, adjust **MarketPrice** / **TimestampedAnnouncement** guards — parameter tuning rather than taxonomy churn.
- Longer term: AI-assisted submission helpers may improve observation quality without necessarily expanding the paradigm count.
- Integration targets: prediction venues, futarchy-style DAOs, **RWA** / parametric insurance hooks.

**Implementation & integration.** Platform-builder detail lives in supporting docs — treat this section as orientation, not a commitment timeline.

### 9. Conclusion

If there is a single idea worth carrying away, I think it is this: **specialize where the mechanism of truth differs; reuse simple machinery everywhere else.**

Fixing five paradigm-level strategies instead of chasing topic churn is a bet — not a guarantee — that most liquid markets reuse a small number of resolution **shapes**, and that the rest can live inside an explicit, conservative long-tail paradigm with heavy **No** bias and high stakes where appropriate.

Prediction markets might remain niche; they might become serious risk-transfer infrastructure. Either way, **oracle design** shapes who gets paid, who gets rugged by ambiguity, and how much dispute friction markets tolerate. PmOracle is one proposal in that design space: comparatively minimal plumbing, paradigm-specific guards, and honesty about edge cases (overlap, governance tuning, possible future paradigms).

The open work is empirical — backtests, live dispute rates, and calibration — more than slogans.

---

## Appendix A: Paradigm Decision Tree

**How to select a paradigm:** Ask "What is the fundamental way truth gets established for this question?" Then map to paradigm:

| If truth comes from… | Select |
|----------------------|--------|
| Official institution (govt, court, regulator) | AuthorityCertified |
| Aggregated price or financial feed | MarketPrice |
| Public announcement (timing matters) | TimestampedAnnouncement |
| Numeric sensor or official measurement | ParametricMeasurement |
| Meme, cultural, interpretive, or long-tail | SubjectiveConsensus |

**How to create a market:** (1) Ask the question above. (2) Map to paradigm via the table. (3) Submit a market-creation request with paradigm, market identifier, and resolution rules. (4) Agents submit observations; coordinator resolves at deadline. (5) Contracts retrieve the resolved outcome to settle.

---

## Appendix B: Backtesting & Real-World Validation

**Target validation (post-launch):** Backtest PmOracle resolution logic against actual 2025–2026 Polymarket/Kalshi resolutions to measure:

| Metric | Target | Method |
|--------|--------|--------|
| Win rate (correct resolution) | &gt;98% | Compare paradigm-strategy output vs. platform final outcome |
| Manipulation resistance | Low dispute rate | Track dispute frequency by paradigm; compare to optimistic oracle baselines |
| Favorite-longshot bias | Reduced vs. naive | Measure calibration by paradigm; compare to 2025–2026 Kalshi/Polymarket data |

**Placeholder for results:** Once PmOracle is deployed and historical data is available, this appendix will be populated with backtesting results (e.g., "AuthorityCertified: 99.2% match with BLS final; MarketPrice: 0.1% dispute rate on crypto thresholds"). Supporting docs: `resolutions.md`, `bias-business.md`.