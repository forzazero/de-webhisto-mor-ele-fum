**Market Making is Dead: Chakraborty & Kearns 2011 Finally Killed by Realistic Markets**

### Abstract

Chakraborty & Kearns (2011) prove that a simple symmetric limit-order market maker earns expected profit that grows linearly in time when the mid-price mean-reverts: local spread capture from volatility dominates squared inventory drift at long horizons. That result is intentionally stylized—it omits fees, toxicity, stochastic order flow, and most of what defines modern electronic markets. This note stacks two further layers on top of that baseline. **Second**, Avellaneda & Stoikov (2008) and the post-2011 control literature recast market making as stochastic optimal control in a limit order book with Poisson intensities \(\lambda(\delta)=Ae^{-k\delta}\). We recall the reservation-price solution, record a structured **Hamilton–Jacobi–Bellman** template (diffusion plus controlled bid/ask jumps), and explain why maker rebates enter through per-fill cash increments so that optimal spreads tighten—and why exponential arrival laws imply a sharp **volume** response to small changes in quoted distance. **Third**, at the **exchange** level, sustainable liquidity requires self-funded maker–taker economics: net fee per print \(R_{\text{exchange}}=(r_t+r_m)V\) should stay positive on average while programs use volume share, spread/depth quality, and phased bootstraps. We summarize six major platforms (2025–2026 practice), a compact playbook, and supporting evidence (Malinova & Park; Yagi et al.; Hoshino, Mizuta & Yagi; Harris; El Euch, Mastrolia, Rosenbaum & Touzi; plus current venue documentation). The title is polemical: Kearns is not refuted, but **deployment**—quoting, fees, and principal–agent design—requires the full stack.

### Introduction

The phrase “market making is dead” is not a claim that **inventory risk** or **volatility harvesting** disappeared; mean reversion and spread capture remain central intuitions. It is a claim about **where the hard problem moved**. In a frictionless, discrete-time mean-reverting world, a ladder of passive quotes and frequent refresh is enough to make expected utility grow with the horizon. In production, the bottleneck is **jointly** modeling the book (optimal distance to mid, skew, fill risk), the exchange’s **fee menu** (who pays whom), and the **solvency** of rebates when the venue also wants growth.

This note is written for readers who already care about one of the three pieces—microstructure theory, **Avellaneda–Stoikov** (AS) execution, or **crypto/CEX** fee competition—and want a single thread that connects them. The progression is deliberate:

1. **Kearns-style MM** — a clean benchmark for *why* providing liquidity can be profitable when prices lack a strong drift.
2. **AS and extensions** — continuous-time control with exponential arrival rates; fees and rebates as shifts in effective per-fill economics; an explicit HJB layout linking rebates to \(\delta^*\) and to fill intensity.
3. **Venue economics** — designated incentives, maker–taker schedules, representative platform policies, a survival playbook, and a mathematical restatement of self-funding (\(r_t+r_m>0\) on average) together with empirical citations.

Modern extensions (Guéant–Lehalle–Fernandez-Tapia, Hawkes flow, adverse-selection adjustments, reinforcement learning) appear where they clarify what working desks implement; sell-side **algo-MM** decks that target classical inventory trajectories without modeling **exchange fee design** are flagged as the wrong tool for the third layer. The **conclusion** returns to the main tension: micro optimality for one desk does not imply **venue** sustainability unless taker flow finances the policies that attract quotes in the first place.

Optimal market making in realistic markets still builds on the spirit of Kearns (“Market Making and Mean Reversion”), but the extensions above—inventory risk aversion, stochastic order flow, adverse selection, fees, latency, and impact—are what make the problem **operational**.

### Quick recap of the Kearns paper
The paper proves that a **very simple symmetric MM strategy** is profitable in mean-reverting price processes (e.g., Ornstein-Uhlenbeck). The MM posts a buy limit order at \(P_t - 1\) and sell at \(P_t + 1\) (ladder depth \(C\) to handle jumps), refreshing frequently. Profit decomposes exactly as:
\[
\text{Profit} = K - \frac{z^2}{2}
\]
where \(K = \sum |P_{t+1} - P_t|\) (total local price movement = spread capture from volatility) and \(z = P_T - P_0\) (net directional change = inventory risk at forced liquidation). Mean reversion keeps \(E[z^2]\) bounded while \(E[K] \propto T\), so expected profit grows linearly with time—even under realistic constraints like refreshing only every \(L\) ticks.

This is a **theoretical insight**: MM profits from volatility, not direction, and mean reversion naturally hedges inventory. Limitations (explicitly noted in the paper): no market impact, exogenous price, no adverse selection from informed traders, perfect observability, no fees/latency.

### How optimal MM works in realistic (HFT / limit-order-book) markets
The **seminal practical framework** is Avellaneda & Stoikov (2008) “High-frequency trading in a limit order book.” It turns the problem into stochastic optimal control and is still the industry baseline (with hundreds of extensions).

**Core setup** (continuous-time):
- Mid-price \(S_t\) follows diffusion (usually Brownian motion with volatility \(\sigma\)).
- Buy/sell market orders arrive as independent Poisson processes with intensity that decreases with distance from the quote:
  \[
  \lambda(\delta) = A e^{-k \delta}
  \]
  (\(A, k > 0\): market depth / price sensitivity parameters, calibrated from LOB data).
- MM has inventory \(q_t\) (positive = long) and maximizes expected utility of terminal wealth at horizon \(T\) (often exponential/CARA utility \(U(x) = -e^{-\gamma x}\) with risk aversion \(\gamma\)).

**Optimal policy** (two-step):
1. Compute the **reservation (indifference) price** \(r(q,t)\) — the price at which the MM is indifferent between posting and not posting:
   \[
   r(q,t) \approx S_t - q \gamma \sigma^2 (T-t) - \frac{1}{\gamma} \ln\left(1 + \frac{\gamma}{k}\right)
   \]
   (the term \(-q \gamma \sigma^2 (T-t)\) **skews** quotes aggressively toward the side that reduces inventory risk).
2. Add optimal spreads \(\delta^{b*}, \delta^{a*}\) around the reservation price (derived from the intensity \(\lambda(\delta)\)) so the MM is compensated for (i) adverse selection / fill risk and (ii) remaining inventory risk.

**Intuition of the optimal strategy**:
- When \(q \approx 0\) (flat inventory) → post roughly symmetric quotes around the mid, capturing the spread.
- When \(q > 0\) (long) → skew bid/ask **tighter on the ask** (sell aggressively) and wider on the bid to encourage unwinding.
- The further from \(T\), the more aggressive the skew (more time to manage risk).
- Higher volatility \(\sigma\) or risk aversion \(\gamma\) → wider overall spreads.
- Higher \(k\) (more price-sensitive flow) → tighter spreads.

**Key extensions that make it realistic** (post-2011 research):
- **Guéant, Lehalle & Fernandez-Tapia (2012/2016)**: General modeling framework that reconciles earlier approaches, proves existence/uniqueness of solutions, gives closed-form approximations, and extends to **multi-asset** MM (correlations across inventories). This is what many prop desks actually implement.
- **Persistent/clustered order flow**: Replace Poisson with Hawkes processes (Jusselin et al. 2020) because real markets have self-exciting flows.
- **Adverse selection & informed trading**: Add a “toxicity” signal or micro-price (order-book imbalance, trade-flow) to widen spreads when flow looks informed.
- **Fees & maker-taker rebates**: Critical in practice — many venues pay **negative fees** (rebates) to makers. The model is easily adjusted by shifting the effective spread.
- **Realistic dynamics**: Stochastic volatility, jumps, mean-reversion (ties back to Kearns!), latency, partial fills, market impact for larger players.
- **Model-free / data-driven**: Reinforcement learning (deep RL, actor-critic, etc.) trained on realistic LOB simulators (ABIDES, custom tick-by-tick) outperforms parametric AS when markets are non-stationary.

**Linking AS to the liquidity–volume mechanism.** The Avellaneda–Stoikov setup is not only a quoting recipe: it is the standard **stochastic optimal control** model in which a fee or rebate enters the MM’s cash process and therefore the **Hamilton–Jacobi–Bellman (HJB)** optimality conditions. The qualitative chain matches the venue-level flywheel later in this note—**rebates → tighter optimal quotes → higher fill intensity → more taker volume → more taker-fee revenue → room for rebates**—with the exponential arrival law supplying the **nonlinear** amplification.

Below, the HJB is written in a **fixed template** (state → dynamics → controls → jump operators → full equation) so the jump structure stays visible; rebates and fees enter only through the **cash increments** \(\Pi^b,\Pi^a\) inside the shifted value \(V(\cdot)\).

**1. State, objective, and primitives**

- **State** \((S_t,X_t,q_t,t)\): mid-price, cash, inventory in **round lots** (discrete \(q\)), time \(t\le T\).
- **Objective** (CARA benchmark): maximize \(\mathbb{E}\bigl[U(X_T+q_TS_T)\bigr]\) with \(U(w)=-e^{-\gamma w}\), \(\gamma>0\).
- **Mid dynamics**: \(dS_t=\sigma\,dW_t\).
- **Controls**: non-negative quote distances \((\delta^b_t,\delta^a_t)\) from the chosen reservation construction (bid/ask posted symmetrically around the reservation price as in **Optimal policy** above).
- **Controlled intensities** (independent Poisson streams):
\[
\lambda^b(\delta^b)=A\,e^{-k\delta^b},\qquad \lambda^a(\delta^a)=A\,e^{-k\delta^a},\qquad A>0,\;k>0.
\]

**2. Jump conventions (passive fills)**

Write \(V=V(S,X,q,t)\) for the optimal **value function** (certainty equivalent under the exponential transformation). On a **passive bid** fill the MM buys one unit: \(q\mapsto q+1\) and cash jumps by a **model-specific** increment \(\Pi^b=\Pi^b(\delta^b)\) (half-spread, fees, maker rebate, etc.). On a **passive ask** fill the MM sells one unit: \(q\mapsto q-1\) with cash increment \(\Pi^a=\Pi^a(\delta^a)\).

**3. Jump generators (bid and ask)**

Define the **nonlinear jump operators** acting on \(V\):

\[
\mathcal{B}(V)=\max_{\delta^b\ge 0}\;\lambda^b(\delta^b)\Bigl[V\bigl(S,X+\Pi^b(\delta^b),q+1,t\bigr)-V(S,X,q,t)\Bigr],
\]
\[
\mathcal{A}(V)=\max_{\delta^a\ge 0}\;\lambda^a(\delta^a)\Bigl[V\bigl(S,X+\Pi^a(\delta^a),q-1,t\bigr)-V(S,X,q,t)\Bigr].
\]

Each line is one **Hamiltonian piece**: intensity × (value after jump − value before jump), maximized over the corresponding distance.

**4. Full HJB (structural display)**

Coupling Brownian diffusion in \(S\) with the two controlled Poisson sides gives the **backward (dynamic programming) PDE** in the Markov state \((S,X,q)\):

\[
\boxed{
\;0=
V_t
+\tfrac12\sigma^2 V_{SS}
+\mathcal{B}(V)
+\mathcal{A}(V)
\;}
\]

with \(\mathcal{B},\mathcal{A}\) as above. Expanded (same content, explicit maxima):

\[
\begin{aligned}
0={}& V_t(S,X,q,t)+\tfrac12\sigma^2 V_{SS}(S,X,q,t) \\
&+\max_{\delta^b\ge 0}\Bigl\{\lambda^b(\delta^b)\Bigl[V(S,X+\Pi^b(\delta^b),q+1,t)-V(S,X,q,t)\Bigr]\Bigr\} \\
&+\max_{\delta^a\ge 0}\Bigl\{\lambda^a(\delta^a)\Bigl[V(S,X+\Pi^a(\delta^a),q-1,t)-V(S,X,q,t)\Bigr]\Bigr\}.
\end{aligned}
\]

**5. Reduction to quotes (\(\delta^{b*},\delta^{a*}\))**

Under the exponential **ansatz** \(V=-\exp\bigl(-\gamma(X+\theta(S,q,t))\bigr)\), the PDE above reduces to an equation for the **reduced value** \(\theta\); first-order conditions in \(\delta^b,\delta^a\) together with the reservation map recover the closed-form approximations already stated under **Optimal policy** (skew \(-q\gamma\sigma^2(T-t)\) and the \(\ln(1+\gamma/k)\) correction). Any **per-fill** rebate shifts \(\Pi^{b,a}\), moves the interior first-order conditions, and therefore moves the optimal distances—this is the precise sense in which fees enter **inside** the maxima, not as an afterthought.

*(How rebates enter.)* A maker rebate \(r_m<0\) (or any deterministic **per-fill** cash transfer to the liquidity provider) enters the wealth dynamics schematically as an additive term on executed limit orders, e.g. \(dX_t = \cdots + (\text{per-fill gross edge} + r_m)\,dN_t\). That shifts the MM’s **indifference** conditions in the HJB: holding risk aversion and horizon fixed, the optimizer is willing to post **smaller** gross half-spreads because **net** profit per fill rises, so
\[
\delta^*_{\text{rebate}} < \delta^*_{\text{no-rebate}}
\]
under the usual regularity of the interior solution. **El Euch, Mastrolia, Rosenbaum & Touzi (2021)** (*Mathematical Finance*, “Optimal make–take fees for market making regulation”) make this layer explicit: an **exchange** chooses make–take fees as a **principal–agent** problem built on the Avellaneda–Stoikov MM layer, i.e. fee design is not bolted on after the fact—it moves the same \(\delta^*\) the MM solves for.

*(Exponential volume multiplier.)* Because \(\lambda(\delta)=A e^{-k\delta}\), a small inward move \(\Delta\delta>0\) in optimal distance multiplies intensity by
\[
\frac{\lambda(\delta-\Delta\delta)}{\lambda(\delta)} = e^{k\,\Delta\delta}.
\]
So rebate-driven tightening of \(\delta^*\) raises expected fills **exponentially in \(k\Delta\delta\)** at the intensive margin; aggregating across competitive makers is the formal sense in which “a little tighter” can support **large** increases in taker-touching flow when \(k\) is large. Taker-fee revenue scales with that flow while venue solvency still requires **\(r_t+r_m>0\)** on average (the identity in *Mathematical and empirical support*)—the AS block explains **why volume responds sharply** to a calibrated rebate; the venue block pins **when** the response is profit-preserving.

Empirically, make–taker venues exhibit the joint prediction—**narrower quotes and higher activity** once rebates clear participation thresholds—consistent with Malinova & Park (2015), Yagi et al. (2020), and the exchange snapshots later (Hyperliquid, Binance MM programs, etc.).

In production, MMs also:
- Calibrate \(A, k, \sigma\) live from recent LOB/trade data.
- Run multiple strategies in parallel (different risk tolerances or horizons).
- Hedge inventory in correlated instruments or futures.
- Monitor “toxic flow” and pull quotes during news.

Everything above assumes an MM **chooses** to trade a venue once the microstructure is attractive. **Venue operators** still set the table: fee schedules, rebates, obligations, and launch subsidies that make that participation rational when volume is thin.

### Research on attracting market makers to a **new** exchange
There is **substantial** research (and real-world practice) on bootstrapping liquidity via incentives. New exchanges (or new listings/segments) face a chicken-and-egg problem: without liquidity, no traders; without traders, no MM profits.

**Main mechanisms studied and used**:
- **Maker-taker (or taker-maker) fee schedules**: Makers get rebates (often 10–30 bps), takers pay. Funded by taker fees. Strongly favors MM. Empirical work shows this dramatically increases liquidity provision.
- **Designated Market Maker (DMM) programs**: Exchange appoints (or lets firms contract with) official MMs for specific symbols. Obligations (max spread, min depth, uptime % during trading hours) + rewards (rebates, fixed payments, fee waivers, priority). Competition among multiple DMMs per stock further tightens spreads.
  - Evidence: NYSE Euronext Paris, China’s STAR Market (2022 introduction), small-cap programs, crypto perpetuals.
  - Contracts often include KPIs; breaches incur penalties.
- **Performance-based incentives & retainers**: Volume tiers, spread/quote-quality bonuses, sometimes direct cash retainers (especially in crypto or new venues).
- **Bundled incentives**: For thinly-traded stocks, exchanges subsidize MM on illiquid names using profits from liquid ones.
- **Technical & operational**: Ultra-low-latency colocation, reliable APIs, market-data feeds, test environments — MMs won’t commit capital without this.
- **In crypto / DEX context**: Concentrated liquidity (Uniswap v3 ranges), yield farming, fee splits, LP token incentives, partnerships with professional MMs (Wintermute, Jump, etc.).

**Empirical findings**:
- Introducing DMMs or increasing competition among them reliably narrows spreads and improves depth, especially in smaller/younger markets.
- Optimal rebate levels exist — too low → no MMs; too high → lazy quoting.
- Public-policy angle: some regulators offer tax breaks to firms that hire DMMs (e.g., Chile 2012 reform).

In short: Kearns gives the **why** (volatility + mean reversion = free lunch if you manage inventory). Avellaneda-Stoikov + Guéant give the **how** (dynamic, risk-aware quoting in real LOBs). To launch a new exchange, you need **explicit economic incentives** (rebates + DMM contracts) plus rock-solid tech — this is well-documented in both academic literature and exchange rulebooks.

The sections above treat **market makers as agents** optimizing quotes and inventory. The sections below treat the **exchange as principal**: fee schedules and programs must attract those agents while keeping matched trades **net fee-positive** for the venue at scale. What follows is that layer—current platform practice, a playbook, then definitions and citations that back the sustainability claims.

### Sustainable maker-taker rebates: policy, practice, and evidence

**Self-funding maker-taker economics.** Across traditional finance, mature CEXs, and high-growth DEXs, the recurring pattern is the same: taker fees on aggressive flow finance maker-side rebates or discounts so the venue is not permanently subsidizing liquidity out of pocket. That yields a flywheel—deeper books → more taker volume → more taker revenue → room for rebates and retained margin—provided the arithmetic per trade stays in the venue’s favor (formalized in *Mathematical and empirical support* below).

**Scope note (sell-side algo-MM decks).** The Morgan Stanley / Meridian Point Group ALMM.pdf is **not directly useful for fee design**: it targets traditional algo market-making (liquidity zones, night-effect pricing, spread capture), not exchange maker–taker schedules, rebate sustainability, or crypto-native programs. It still supports one premise used above—that committed capital needs strong economic incentives—which is why venues layer rebates and tiers on top of raw microstructure.

#### Policies from the example exchanges (2025–2026 data)

Here is how six platforms structure rebates/rates in practice:

- **Hyperliquid (DEX, highly successful growth story)**: Fully open/permissionless MM—no DMM program, no special deals, no latency advantages. Public tiered maker rebates based on 14-day rolling maker volume share. Base perps: maker +0.015% / taker 0.045%. Top tiers reach **−0.003% rebate** (exchange pays you). Rebates paid continuously/real-time to wallet. Aligned quote assets get 50% better rebates. Extremely bot-friendly; drove explosive volume via accessible HFT/MM strategies.

- **Lighter (zk-DEX)**: Aggressive bootstrap via **zero fees** for standard accounts (maker/taker 0%, but higher latency 200–300 ms). Premium accounts (opt-in via LIT staking) pay small maker fees with discounts + ultra-low latency. Dedicated **Liquidity Partner Program** uses randomized order-book snapshots to reward *quality* liquidity (tight spreads + depth), not pure volume—e.g., $250k weekly cash pools targeted at RWAs. Points program allocates 20% to MM activity + funding rebates. Hybrid zero-fee retail + performance cash for pros.

- **BitMart**: Standard VIP tiers (30-day volume + BMX holdings + cashback discounts). Dedicated **Market Maker Program** offers industry-leading negative maker rates (especially first 2 months), fast-track VIP via volume proof from other exchanges, MM referrals (both sides get extra discounts), and 24/7 support. Base futures maker often ~0.02% before rebates/cashback.

- **OKX**: Classic VIP tiers based on assets OR 30-day trading volume (relatively low entry barriers). High VIP levels deliver **negative maker fees/rebates** (e.g., −0.0075% or better in top tiers). Dedicated MM support (higher limits, priority). Rebates paid in the traded asset.

- **Gate.io**: One of the **most aggressive** in the industry. Separate MM tiers + VIP tiers (now volume-only, no mandatory GT holdings). Maker rebates as low as **−0.012%** in MM tiers; **0% maker fee** at VIP 10+. Extra institutional perks (loans, prize pools, interest-free funding). Designed for fast liquidity acquisition.

- **Binance**: Mature hybrid model. Standard VIP (volume + BNB holdings) already gives low/zero maker fees. Dedicated **Liquidity Provider / Market Maker programs** (spot, futures, fiat, new listings) pay extra rebates **tied to % of total maker volume contributed** (e.g., −0.005% to −0.012%+). Temporary zero-maker promos on new/illiquid pairs. Performance-based (some programs weight spread/depth). Rebates often weekly or real-time.

#### Sustainable solutions that actually help an exchange grow and survive

The exchanges above show a clear evolution from “growth-at-all-costs” to self-sustaining models. Here are proven, long-term viable approaches:

1. **Self-funding maker-taker spread (the #1 rule)**  
   Taker fees (0.04–0.35%) must exceed average maker rebates paid. Hyperliquid’s structure (taker ~0.045% vs. max rebate −0.003%) is a textbook example: net positive per trade, rebates act as a “marketing cost” funded by volume growth. Exchanges that violate this (pure subsidies without taker revenue) eventually cut programs or die. The identity \(R_{\text{exchange}} = (r_t + r_m) V\) in *Mathematical and empirical support* states the same constraint in one line.

2. **Tiered + performance/quality-based rebates (not blind volume)**  
   - Volume-share tiers (Hyperliquid, Binance % maker contribution) reward genuine liquidity providers.  
   - Quality metrics (Lighter’s order-book snapshots for depth/spread) prevent wash-trading farming.  
   - Obligations for top rebates (uptime %, max spread, min size) — common in Binance/Gate.io MM programs.  
   This avoids “lazy quoting” and ensures rebates buy *real* liquidity.

3. **Public/open tiers + targeted bootstrap incentives**  
   Hyperliquid proves anyone-can-earn rebates scale extremely well (bot explosion).  
   For new/illiquid pairs: temporary high rebates, zero-maker periods, or dedicated cash pools (Lighter $250k/week, Gate.io loans, Binance new-listing promos). Phase them out as organic volume arrives.

4. **Hybrid incentives (rebates + tokenomics + extras)**  
   - Token staking/utility for extra discounts (Lighter LIT, BitMart BMX, Binance BNB).  
   - Points/airdrop allocation to MMs (Lighter 20%).  
   - Protocol revenue share (Hyperliquid HLP vault, fee buybacks).  
   - Non-fee perks: low-latency APIs, dedicated support, interest-free loans, higher position limits (Gate.io, OKX, BitMart).

5. **Phased approach for survival**  
   - **Month 0–6**: Aggressive rebates + cash/points (Gate.io/Lighter style) to solve chicken-and-egg.  
   - **Month 6+**: Transition to volume/quality tiers + self-funding (Hyperliquid/Binance model).  
   - Monitor net fee capture; dynamically adjust rebate caps or pair eligibility.

**Bottom line for a new exchange**: Copy Hyperliquid’s transparent, open, volume-share rebate tiers for broad participation plus Lighter’s quality snapshots for efficiency. Keep rebates funded by taker fees (aim for rebate < 10–30% of taker rate at scale). Add token alignment and targeted cash for new markets. This combination has proven to grow volume sustainably without destroying unit economics. The exchanges that survive long-term are exactly the ones that treat rebates as a temporary liquidity subsidy, not a permanent loss-leader.

#### Mathematical and empirical support

This block formalizes the **#1 rule** in the playbook and the **self-funding** claim in the opening of this section: same economics, explicit notation, and literature that tests the mechanism rather than desk-level alpha.

**1. Core sustainability equation: self-funding maker-taker net revenue**

The **net fee to the venue per matched trade** (ignoring payment rails and cross-subsidies) is:

\[
R_{\text{exchange}} = (r_t + r_m) \times V
\]

where:

- \( r_t > 0 \): taker fee rate (e.g., 0.045% or 0.00045)
- \( r_m \): maker fee rate (negative when it is a rebate, e.g., −0.003% or −0.00003)
- \( V \): notional trade value

Sustainability at scale means \( R_{\text{exchange}} > 0 \) **on average** across flow: taker revenue must cover maker rebates plus whatever else the venue must fund. Polymarket, Kraken, Hyperliquid, and similar venues describe maker programs as funded from taker-side fees for exactly this reason.

**Numerics (Hyperliquid, 2026, matching the policy bullet above)**:

- Base perps: \( r_t = 0.045\% \), \( r_m = +0.015\% \) → \( R_{\text{exchange}}/V = 0.060\% \)
- Top maker-rebate tier: \( r_m = -0.003\% \) → \( R_{\text{exchange}}/V = 0.042\% \)

Binance-style MM tiers (rebates up to about −0.012%), Gate.io, OKX, etc., keep **taker** schedules above **effective maker** payouts so the same inequality holds in equilibrium.

**2. Flywheel / volume-growth equation**

The incentive flywheel is captured in simple market-share dynamics from agent-based simulations:

\[
\text{Market share}_{t+1} = f(\text{rebate level}) \times \text{liquidity depth}
\]

where higher rebates → deeper order book → lower effective spreads → more taker volume → more taker-fee revenue → ability to sustain/raise rebates. Empirical papers confirm this loop:

- When rebates are “sufficient” (typically 10–30% of taker fee), the maker-taker venue gains market share vs. flat-fee venues.

Conversely, zero-fee experiments (e.g., Binance 2022) show the opposite: makers widen spreads, depth falls, and total transaction costs rise for takers.

**3. Academic and empirical supporting references**

These are the key peer-reviewed / exchange-backed papers and documents this note draws from:

- **Malinova & Park (2011/2015)** — “Subsidizing Liquidity: The Impact of Make/Take Fees on Market Quality” (*Journal of Finance*). First major empirical study (Toronto Stock Exchange rebate introduction). Found tighter spreads, deeper books, higher volume, and increased competition among liquidity providers. Holding total fee constant, the *breakdown* into rebate vs. taker fee matters.
- **Yagi et al. (2020)** — arXiv:2010.08992, “Analysis of the impact of maker-taker fees on the total cost of a taking order” (artificial-market simulation). Equation for total taker cost includes market impact + fees; shows maker-taker reduces volatility and impact but raises raw taker cost — hence the need for net-positive \( r_t + r_m \).
- **Hoshino, Mizuta & Yagi (2021/2022)** — JPX Working Paper & *Journal of Computational Social Science*. Two-market simulation: maker-taker venue’s volume share rises with rebate size *if* rebates are funded by taker fees. Exact condition for out-competing flat-fee venues.
- **Harris (2013)** — “Maker-Taker Pricing Effects on Market Quotations”. Adjusted-quotation equations:
  \[
  \text{Effective bid/ask value} = \text{quoted price} \pm \text{fee/rebate adjustment}
  \]
  (explicit derivation for how rebates shift displayed depth).
- **El Euch, Mastrolia, Rosenbaum & Touzi (2021)** — “Optimal make–take fees for market making regulation,” *Mathematical Finance* 31(1), 109–148. Principal–agent formulation on top of Avellaneda–Stoikov: the exchange designs compensation so the MM’s optimal quotes **tighten**, improving liquidity and trading costs vs. no contract.
- **Industry confirmation (2025–2026)**: Polymarket docs (“Maker Rebates funded by taker fees… redistributed proportionally”), Kraken maker-fee program updates (“optimizing liquidity incentives” to remain sustainable), and Hyperliquid/Gitbook fee tiers.

Together, the identity \(R_{\text{exchange}} = (r_t + r_m)V\), the **Linking AS** subsection (HJB + \(e^{k\Delta\delta}\) intensity), and the papers above are the formal stack: **desk-level** control shifts \(\delta^*\) when per-fill economics change; **exponential** \(\lambda(\delta)\) turns small spread changes into large fill-rate responses; the **venue** still needs \( r_t + r_m > 0 \) on average if rebates are not pure loss-leaders. Tiered volume shares, spread/depth tests, and phased promos (the playbook) then align incentives so that inequality survives gaming and thin markets.

### Conclusion

The note’s arc is **benchmark → control → institution**. Kearns answers whether a stripped-down passive strategy can earn in a mean-reverting tape: yes, under strong assumptions, with profit tied to local variation rather than direction. Avellaneda–Stoikov answers how a risk-averse MM should **distance** quotes from a reservation price when fills arrive stochastically and inventory matters; the structured HJB shows **fees and rebates** entering exactly where the optimizer compares value before and after a jump, which is why small changes in per-fill economics can move posted liquidity a lot when \(k\) is large. None of that, by itself, guarantees that an **exchange** can pay negative maker fees forever: the venue layer adds a **budget**—taker revenue and discipline on tiers—so that the flywheel is funded rather than hallucinated.

For a new or growing venue, the actionable synthesis is conservative and consistent with both theory and recent practice: keep **average** net fees per matched trade positive; use **open or tiered** maker programs so real depth earns the subsidy; add **quality** screens where volume alone is gameable; treat aggressive bootstrap subsidies as **temporary**; and align token, points, or loan perks so they reinforce, rather than replace, taker-funded rebates. The empirical and principal–agent literature cited above supports each of those levers; the AS machinery explains **why** rebates move activity when they are calibrated rather than maximal.

What remains outside this survey—on purpose—is full identification of causal rebate elasticities venue-by-venue, equilibrium with strategic multi-MM interaction at scale, and regulatory treatment of retail payment for order flow. Those are natural extensions once the **three-layer** map here is shared vocabulary between researchers, operators, and desk quants.

### References

#### Scholarly and technical sources

Avellaneda, M., & Stoikov, S. (2008). High-frequency trading in a limit order book. *Quantitative Finance*, 8(3), 217–224. https://doi.org/10.1080/14697680701381228

Chakraborty, T., & Kearns, M. (2011). Market making and mean reversion. In *Proceedings of the 12th ACM Conference on Electronic Commerce (EC ’11)* (pp. 307–314). ACM. https://doi.org/10.1145/1993574.1993622

El Euch, O., Mastrolia, T., Rosenbaum, M., & Touzi, N. (2021). Optimal make–take fees for market making regulation. *Mathematical Finance*, 31(1), 109–148. https://doi.org/10.1111/mafi.12295

Guéant, O., Lehalle, C.-A., & Fernandez-Tapia, J. (2013). Dealing with the inventory risk: a solution to the market making problem. *Mathematics and Financial Economics*, 7(4), 477–507. https://doi.org/10.1007/s11579-012-0087-0 (see also arXiv:1105.3115.)

Harris, L. (2013). *Maker–taker pricing effects on market quotations* (Working paper, rev. Nov. 2014). USC Marshall School of Business. (Widely circulated; cited in regulatory and industry memos on access fees and liquidity rebates.)

Hoshino, M., Mizuta, T., Sudo, Y., & Yagi, I. (2022). Impact of maker–taker fees on stock exchange competition from an agent-based simulation. *Journal of Computational Social Science*, 5(2), 1323–1342. https://doi.org/10.1007/s42001-022-00169-5

Jusselin, P. (2020). Optimal market making with persistent order flow. *arXiv* preprint arXiv:2003.05958. https://arxiv.org/abs/2003.05958

Malinova, K., & Park, A. (2015). Subsidizing liquidity: The impact of make/take fees on market quality. *The Journal of Finance*, 70(2), 509–536. https://doi.org/10.1111/jofi.12156

Yagi, I., Hoshino, M., & Mizuta, T. (2020). Analysis of the impact of maker-taker fees on the total cost of a taking order. *arXiv* preprint arXiv:2010.08992. https://arxiv.org/abs/2010.08992

#### Industry and venue documentation (accessed 2025–2026)

Hyperliquid documentation (fee tiers and maker rebates). https://hyperliquid.gitbook.io/hyperliquid-docs

Kraken (public announcements and help-center pages on maker incentives and fee updates).

Polymarket documentation (maker rebates funded from taker fees).

The Morgan Stanley / Meridian Point Group *ALMM* deck (cited in the main text only as an example of **sell-side** algo market-making material—not a source on exchange fee schedules).

Platform fee and market-maker program descriptions for **Binance**, **BitMart**, **Gate.io**, **Lighter**, and **OKX** as summarized in the policy section above (terms change frequently; verify live pages before relying on numbers in production).
