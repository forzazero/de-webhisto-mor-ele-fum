# Bias in Prediction and Betting Markets

**Abstract.** Systematic departures from rational probability assessment—*biases*—arise from cognitive limitations, market microstructure, and incentives. This note collects mechanisms documented across psychology and betting-market empirics, then connects them to measurable trading behavior: execution rates, inter-trade timing, and rule-based entry thresholds on deployed automated strategies. The goal is a single reference for *why* biased order flow can be persistent and *how* it may appear in frequency and gating of trades.

---

## 1. Introduction

Biases in prediction markets and betting markets generally are not idiosyncratic “mistakes” but **stable, directional deviations** from well-calibrated probabilities. They combine:

- **Cognitive and affective regularities** (e.g., overweighting small probabilities, overweighting recent news),
- **Market microstructure** (thin liquidity, heterogeneous information, social amplification), and
- **Participant incentives** (recreational demand for action, stories, and lottery-like payoffs).

When those forces align, the resulting mispricings can be **durable enough** to support liquidity provision or contrarian rules—provided one models not only *average* prices but *who* trades *when* and *under which gates*.

The remainder of the document proceeds in two layers: **(A)** a concise catalog of major bias families with intuition and market manifestations; **(B)** empirical summary statistics and an entry-threshold model for a concrete multi-bot setup (30-day window), illustrating how strategy design turns abstract bias into **observed trade density and explicit filters** on market-implied probabilities.

---

## 2. Core retail patterns

### 2.1 Favorite–longshot bias

Retail participants **overweight longshots** (e.g., very cheap contracts) and **underweight favorites**. Empirically, low-priced “Yes” legs often resolve positively less often than their prices imply, so the complementary side can earn a **structural premium**. Drivers include:

- **Probability misperception** (Prospect Theory): small true probabilities are felt as larger; large probabilities are underweighted.
- **Skew-seeking and lottery demand**: a small stake for a huge payoff attracts flow even at negative simple EV in expectation.
- **Heterogeneous beliefs and limits to arbitrage**: “Optimistic” flow piles into longshots; capacity and risk limits prevent full compression of mispricing.

Guards that systematically **fade** extreme longshots (or buy “No” at appropriate prices) are explicit implementations of this edge.

### 2.2 Yogi–Berra bias (late-event overoptimism)

In events approaching resolution, prices for the **trailing** side can remain **too high** versus true comeback odds—the “it ain’t over till it’s over” effect. Causes include **overreaction to salient possibilities** (availability), **narrative bias**, and **emotional salience** of a dramatic reversal. Historical prediction-market and sports data show such inflation near the end of contests. Late-game or late-window **filters** target exactly this stickiness in longshot prices.

### 2.3 “Nothing ever happens” and base-rate neglect

Many provocative markets—novel scandals, disruptions, or one-off events—**resolve negatively** more often than casual intuition suggests. Asking *whether* a dramatic event can happen **primes** scenarios for how it *could* occur, **inflating** subjective probability while **underusing** the base rate that *most* such propositions do not land “Yes.” Cross-platform data often show a **pro-Yes** tilt in crowd forecasts; a disciplined **default toward “no”** on specific market classes is a direct counterweight.

### 2.4 Recency bias and overreaction to recent information

Traders **overweight** the latest news, streaks, or short samples and **underweight** long-run base rates. The **availability heuristic** makes recent vivid outcomes dominate judgment; the **hot-hand** belief (see §3) often amplifies this. Fading overreaction—especially after **hype- or shock-driven** moves—is a natural complement to base-rate and longshot–favorite frameworks.

### 2.5 Why retail flow can stay exploitable

Several cross-cutting reasons help explain *persistence*:

- **Heuristic, fast** decisions (roughly “System 1”) under stress, entertainment, and short horizons.
- **Selection and incentives**: retail often seeks **action, narrative, and skew**; professional or systematic liquidity can sit on the other side.
- **Structure**: **Zero or low fees** can increase volume and churn; **social media** and **thin** books early in a market extend mispricings.
- **Optimism and wishful thinking** in politicized or identity-relevant events.

None of this implies easy profits—execution, adverse selection, and model risk matter—but the biases below are best read as **features of the demand side**, not occasional bugs.

---

## 3. Hot-hand fallacy

The **hot-hand fallacy** is the belief that success runs in “streaks” beyond what independence or a modest true state dependence would justify. Classic work on basketball (e.g., Gilovich, Vallone, and Tversky) showed that after streaks, **conditional** hit rates were not elevated as much as **fans believed**; later work refined this with **selection and difficulty** (harder shots after a streak, etc.).

**Betting and markets:** in-play props, team win streaks, and “momentum” narratives can **compress odds** in favor of recent performance. Momentum *can* be real in some environments, but **belief** in it is often **too strong**—a systematic **fade of streak-chasing** pairs naturally with recency bias (§2.4) and with late-game mispricing (§2.2).

---

## 4. Optimism and pessimism

**Optimism bias** is a tendency to **overestimate** favorable outcomes (and **underestimate** bad ones), often for self-relevant or desirable events. It is **motivationally** and **neurally** well supported: positive information is integrated **more readily** than negative. In markets, it can appear as **excess “Yes”** on exciting or hopeful resolutions—an **“optimism tax”** on narrative-driven underdogs and feel-good events.

**Pessimism bias** is the **mirror image**: overestimating **bad** outcomes; it is **less universal** but appears in depressive or high-uncertainty states, or after **strings of losses**. In trading, it can mean **selling** too hard on **bad** headlines or **underpricing** recoveries. For modeling, the point is not that **one** sign always dominates but that **affective** and **self-serving** processing **bends** prices away from **calibration** in **predictable directions** by market type and regime.

**Interaction with other biases:** optimism stacks with **favorite–longshot** and **halo** (§7); **negativity** (§6) and **Yogi–Berra**-style late fear can create **time-varying** mixes of the two.

---

## 5. Sunk cost fallacy

**Sunk costs** are **irrecoverable** past outlays. Rational **forward-looking** choice should ignore them; in practice, people **stick with** or **add to** losing propositions to **justify** past spending—**loss aversion** and **consistency** drive “throwing good money after bad.”

**Markets and prediction contracts:** **loss-chasing**, **refusal to close** bad legs when news worsens, and **doubling down** to “get back” to even slow **repricing** and keep **stale** beliefs **in the open interest**. That can **lengthen** the life of mispricings that **fading** or **LP** strategies need. **Heuristics** that use **age of OI**, **turnover**, or **stickiness** of volume are indirect ways to detect **sunk-cost-heavy** conditions.

---

## 6. Negativity bias

**Negativity bias** is the tendency for **negative** information, affect, and events to **weigh more** than **positive** ones of comparable magnitude—**threats** are processed **faster** and **remembered** longer. It is **related to** but **distinct from** **loss aversion** and **pessimism** (§4).

**Markets:** after **bad** polls, scandals, or “crisis” frames, **flows into “No”** (or out of “Yes”) can **overshoot**—a **fear** premium. Systematic **contrarian** buying of **depressed** “Yes” (or selling **inflated** “No”) after **spikes** in negative attention is the structural counterpart, subject to event risk. **Recency** (§2.4) and **sunk** attachment (§5) can **prolong** such dislocations.

---

## 7. Halo and horns

The **halo effect** (Thorndike): a **dominant** positive trait or first impression **spreads** to **unrelated** attributes—e.g., inferred competence from likability. The **horns** (or **reverse halo**) effect is the **negative** parallel: one bad trait **taints** the whole.

**Markets:** **celebrity**, **branding**, and **charisma** can **bundle** with **Yes** in politics, sports, and entertainment contracts; one **gaffe** can **over-discount** unrelated dimensions. This **interacts** with **recency** (§2.4), **hot-hand** (§3), and **sunk** reluctance to exit (§5). Heuristic guards might **down-weight** or **fade** “Yes” when a **single personality** **dominates** the narrative, or do the **inverse** when a **one-off** **negative** event **depresses** a **structurally** sound “Yes” (context-dependent).

---

## 8. Curse of knowledge and hindsight bias

**Curse of knowledge:** once a fact is known, it is hard to **simulate** a **less informed** perspective. **Informed** agents may **overestimate** what **retail** knows, **over-explain** prices, or **misconstrue** **liquidity** needs. For **oracles and designers**, the failure mode is **criteria** and **explanations** that are **obvious** ex post but were **not** in real time for many traders.

**Hindsight bias** (“I knew it all along”): after an outcome, people **reconstruct** beliefs as if it were **foreseeable**, **underestimate** ex-ante **uncertainty**, and **judge** others’ **ex ante** **decisions** harshly. In **prediction markets**, it fuels **disputes**, **overconfidence** in the **next** bet, and **narrative** that **reinforces** the same **psychological** **mistakes** (e.g., **favorite–longshot**, **halo**).

**Meta point:** these **distort learning**; biased flow can **remain** if **retail** **repeats** **narrative** and **hindsight-consistent** stories. **Modeling** should treat **post-resolution** **sentiment** and **explanations** with care when **tuning** **guards** or **weights**.

---

## 9. Empirical case: execution density and entry gating

This section summarizes **operational** metrics and **gating** rules in the spirit of **Sections 2.2.1–2.2.2** in the accompanying derivation (see also the figure `bias-strategy-figure.png` in this directory). A fixed **observation horizon** $T$ (days), total executed trades $\mathcal{N}(T)$, and **per-minute** activity tie **abstract** “how often does this strategy act?” to **concrete** numbers.

### 9.1 Execution rate and trade density

**Definitions.** Let $T$ be the **observation period** in **days** (e.g. $T = 30$). Let $\mathcal{N}(T)$ be the **total number of executed** trades in that window. The **mean execution rate** (trades per minute) is

$$
\bar{\lambda} = \frac{\mathcal{N}(T)}{T \cdot 1440},
$$

since $1440$ is the number of **minutes** per day. The **implied inter-trade interval** (in minutes) is

$$
\Delta t = \frac{1}{\bar{\lambda}}.
$$

**Illustrative values** (all three bots, $T = 30$ days):

| Bot | $\mathcal{N}(30)$ | $\bar{\lambda}$ (trades/min) | $\Delta t$ |
| :-- | --: | :--: | :--: |
| Bonereaper | 12,866 | 0.298 | 3.36 min |
| `0xe1D6b514` | 18,915 | 0.438 | 2.28 min |
| `0xB27BC932` | 16,280 | 0.377 | 2.65 min |

**Interpretation:** Higher $\bar{\lambda}$ implies a shorter $\Delta t$—more frequent actions and faster turnover. These statistics do not by themselves signal edge; they describe trading intensity and are useful for comparing gating strictness with PnL or calibration (not reported here).

### 9.2 Entry threshold model

**Logic:** each bot can restrict entries to a window of market-implied (or model-implied) probabilities. Let $q^{(w)}$ denote the relevant implied or quoted value for world or contract $w$, and $\hat p^{(w)}$ a model or subjective probability for the same. Bot $k$ can use an asset-specific interval $[q_{\min}^{(k)}, q_{\max}^{(k)}]$. A schematic entry condition is:

$$
q^{(w)} \in [q_{\min}^{(k)}, q_{\max}^{(k)}]
\;\wedge\;
\hat p^{(w)} - q^{(w)} \geq \varepsilon
\;\wedge\;
p_{j^\* j^\*} \geq \tau,
$$

where:

- $[q_{\min}, q_{\max}]$ limits which levels of implied odds are tradeable;
- $\varepsilon$ is a minimum edge (in probability units) of model over market (or internal over display) required to act;
- $\tau$ is a second threshold on a contextual quantity $p_{j^\* j^\*}$ (e.g. a joint or diagnostic probability in the full model; notation matches the source material).

**Calibrated** **parameters** (illustrative):

| Bot | $[q_{\min}, q_{\max}]$ | $\varepsilon$ | $\tau$ | Assets |
| :-- | :-- | :--: | :--: | :-- |
| Bonereaper | $[0.83,\,0.97]$ | $> 0.03$ | $\geq 0.87$ | BTC, ETH |
| `0xe1D6b514` | $[0.64,\,0.99]$ | $> 0.05$ | $\geq 0.80$ | BTC, ETH |
| `0xB27BC932` | $[0.01,\,0.96]$ | $> 0.05$ | $\geq 0.75$ | BTC, ETH, SOL, BNB, XRP |

**Qualitative comparison:** Bonereaper uses the narrowest $q$-window, which is consistent with less dispersion in admissible states and potentially lower per-trade variance on admissible entries (other risks unchanged). `0xB27BC932` admits the widest range of $q`, which widens the opportunity set and can support a higher trade count, often at the cost of higher per-trade variance if the window includes noisier regions of the implied curve. These tradeoffs echo the themes in §§2–8: stricter rules versus action frequency; theory should align with empirical $\bar{\lambda}$ and risk metrics.

**Figure (screenshot of the same structure):**  
![Execution rate and entry threshold summary](bias-strategy-figure.png)

---

## 10. Synthesis

The cognitive catalog (§§2–8) explains why biased prices and order flow are plausible in competitive but imperfect markets. The operational block (§9) grounds that story in measurable trade frequency and explicit gates on implied levels and edges ($\varepsilon$, $\tau$). Neither layer identifies value on its own; together they constrain how a model of bias maps to what appears in trade logs and rule sets.

---

## References (conceptual)

- Gilovich, T., Vallone, R., Tversky, A. (1985). *The hot hand in basketball: On the misperception of random sequences.*  
- Kahneman, D., Tversky, A. (1979). *Prospect theory.*  
- Tversky, A., Kahneman, A. (1974). *Judgment under uncertainty: Heuristics and biases.*  
- Fischhoff, B. (1975). *Hindsight ≠ foresight: The effect of outcome knowledge on judgment under uncertainty.*  
- Thorndike, E. L. (1920). *A constant error in psychological ratings.*  
- Sharot, T. (2011). *The optimism bias—work on asymmetry in belief updating.*  

*(This is a working note, not a formal bibliography; cite primary sources for publication.)*
