### What is GRVT?

**GRVT** (“gravity”) is a hybrid exchange and investment stack built around **zkSync**’s zero-knowledge rollup tooling (often discussed as part of the **ZK Stack** / appchain framing). The pitch is familiar: combine **CEX-like** matching and UX with **DEX-like** self-custody and stronger cryptographic audit trails than a purely opaque central book.

Launched in **2024**, GRVT is described as an **appchain**—a specialized execution layer connected to the broader zkSync ecosystem—supporting **perpetuals**, **spot**, and (per roadmap) **options**, plus **managed strategies**. Marketing emphasizes **self-custodial** control of keys and funds. Public materials cite **40+** backers, **~$33.3M** raised (names such as **Delphi** and **Hack VC** appear in reporting), and audits by firms such as **NCC Group** and **Spearbit DAO**.

Performance claims in vendor messaging include **sub-millisecond** execution figures and very large trade counts under tight latency thresholds; I would treat those as **benchmark-style** statements worth validating against independent measurement, not as guarantees under stress.

### How GRVT works

#### Core stack

- **ZKsync Validium-style L2**: High-throughput settlement on Ethereum using **zero-knowledge proofs**—pitched as improving **privacy** and **scalability** versus putting every detail on L1. **Interoperability** within zkSync’s constellation is a design goal; “no bridges” is never literally true in complex systems, but the architecture aims to reduce **classic bridge** exposure relative to some alternatives.
- **Hybrid execution**: Fast central-style matching with user-controlled custody—a design that inherits **both** CEX operational surfaces (matching, incident response) and DeFi user-responsibility surfaces (signatures, approvals).

#### Trading flow

- **Onboarding**: External wallets (e.g., MetaMask) and deposits from other venues; messaging suggests **no KYC** for basic retail flows, with **KYB** for strategy managers.
- **Instruments**: Perpetuals with leverage; spot; options described as upcoming. **Negative maker fees** / rebates appear as liquidity incentives.
- **Settlement**: Trades tied to on-chain verification via ZK proofs—exact privacy/audit trade-offs depend on concrete circuit and data-availability choices.
- **Tooling**: Integrations such as **TradingView** for charts and analytics.

#### Strategies and “earn” features

A **strategies marketplace** routes capital to **KYB-verified** managers; examples in materials include **GLP** (a delta-neutral market-making style product) with advertised performance figures (e.g., high APY and Sharpe statistics). **Prime**-style products expose manager portfolios on-chain for transparency claims.

Points, referrals, and promotional **boosts** are standard growth mechanics—their presence tells you something about **go-to-market**, not necessarily about long-run risk-adjusted returns.

#### Custody and security narrative

Self-custody reduces certain **CEX-custody** failure modes; it amplifies **user error** and **phishing** risk. Partnered wallet infrastructure (e.g., **Dfns** in public descriptions) shifts—but does not eliminate—operational burden.

### Limitations and risks

**Smart-contract and implementation risk.** Audits reduce but do not remove vulnerability surface; ZK systems add complexity as well as assurance.

**Hybrid centralization.** Matching and related services can still imply **trusted operators** and **operational security** assumptions distinct from “fully on-chain everything.”

**Privacy versus observability.** Strong privacy can complicate **external monitoring** for subtle failures or insider-adjacent behavior—an unavoidable tension class.

**Performance under stress.** Throughput and fee dynamics under volatility spikes are empirical questions for any L2.

**Regulatory evolution.** “Regulated DEX” positioning may collide with changing **KYC/AML** expectations in major jurisdictions.

**Strategy economics.** High headline yields in promotional copy often embed **hidden leverage**, **liquidity risk**, or **short-sample** backtests—worth treating skeptically.

**Ecosystem dependency.** GRVT’s liveness and upgrade path are entangled with **zkSync** roadmap decisions.

### Major incidents and hypothetical failure modes

Public reporting consulted for this note (including community channels) did not surface a **widely documented protocol-level exploit** comparable to large bridge hacks—plausibly consistent with a **recent mainnet** timeline (**November 2025** in materials) and emphasis on audits. **Absence of headlines is not a security proof**; it is a weak prior.

Retail **liquidations** under leverage are routine market mechanics, not “protocol rekts” in the exploit sense.

Hypothetical stress patterns—generic to perp venues and only loosely GRVT-specific—include:

1. **Leverage cascades** during volatility with thin books.
2. **Oracle lag or manipulation** biasing liquidations or funding.
3. **Liquidity/margin model bugs** or upgrade regressions despite audits.
4. **Incentive-driven wash volume** around points seasons distorting apparent activity.

I think the honest stance is: monitor **primary disclosures**, **incident postmortems**, and **on-chain monitors** rather than inferring safety from marketing copy.

### Using the platform (factual sketch)

Mainnet operation implies **real funds** at risk. Typical flows described in docs: sign-up at official domains, connect a **zkSync-compatible** wallet, bridge assets, trade small size while learning margin rules, and use account security features (**2FA**, hardware wallets where appropriate). API documentation is referenced at vendor doc hosts; **chain IDs** and endpoints should be taken from **current official docs**, not second-hand lists.

### Scale and activity (illustrative; verify live)

Figures below appeared in internal research snapshots around **January 2026** and **will drift**:

| Metric | Indicative range / note |
|--------|-------------------------|
| TVL | ~**$87M** tier (growth from ~**$32M** post-launch cited in some reports) |
| 24h volume | ~**$1.1B–$1.8B** (sources disagree; regime-dependent) |
| 30d volume | ~**$39B** perpetuals-focused (as claimed) |
| Cumulative volume | **>$113B** cited late **2025** |
| Open interest | ~**$508M** cited versus ~**$50M** near launch in some tables |
| Pairs | **74–104** in marketing materials |

Treat trackers as **noisy**; compare definitions (perps vs spot, internal netting vs on-chain settlement).

### Raising TVL and liquidity: stated strategy

Public narratives emphasize **Season 2 points**, **negative maker fees**, **GLP-style vaults** with institutional managers (e.g., **Ampersan** / Optiver-adjacent framing in materials), **earn on idle margin**, roadmap items (**Atlas**, **Aave** integration, **RWA** margin), and **licensing** milestones (e.g., **Upbit SG** references). The pattern is: **real yield** plus **points flywheel** plus **institutional credibility**—each lever has familiar failure modes (yield compression, mercenary capital, regulatory friction).

### Trading competitions and volume

Community-coordinated **competitions** plausibly **boost short-term volume** through leaderboard incentives—often mixing genuine skill with **washer-adjacent** activity. Ecosystem write-ups reference events such as a **May 2025** beta-era retail competition (volume-weighted prizes on the order of **~$175K USDT** in some descriptions), a **December 2025** partner-branded volume campaign (“Wokers” × GRVT on some timelines), and **ROI**-style contests around **mainnet** launch—quantitative attribution to **sustained** liquidity remains hard without internal data.

On balance, competitions are **short-horizon accelerants**; durable depth depends on **market-maker commitments**, **fee structures**, and **product-market fit** beyond campaigns.

### Synthesis

GRVT sits in a crowded **CeDeFi** design space: use ZK and appchain architecture to approach **CEX UX** with **less naked custody risk**, while keeping hard problems—**oracle trust**, **matching fairness**, **regulatory classification**, and **user operational security**—fully in frame. The open question, as with peers, is whether the hybrid can remain **credible** under stress without silently reintroducing the weaknesses it promises to escape.
