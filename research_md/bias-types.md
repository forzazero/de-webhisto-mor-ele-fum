# Bias in Prediction and Betting Markets

**Abstract.** In my view, systematic departures from well-calibrated probability—what I’ll call **biases** here—are less like one-off “mistakes” and more like recurring patterns: they reflect cognitive limits, how markets are built, and what participants are optimizing for. This note gathers mechanisms that show up in psychology and in betting-market empirics, then links them to trading behavior you can actually measure: execution rates, time between trades, and rule-based entry thresholds on deployed automated strategies. The aim is a single place to ask *why* biased order flow can persist and *how* that persistence might show up in how often strategies trade and which gates they use.

---

## 1. Introduction

Biases in prediction and betting markets are often **stable, directional deviations** from rational probability assessment—not random noise around the truth. On balance, they seem to come from three buckets:

- **Cognitive and affective regularities** (for example overweighting small probabilities or salient recent news),
- **Market microstructure** (thin liquidity, uneven information, social amplification), and
- **Participant incentives** (recreational demand for action, stories, and lottery-like payoffs).

When those forces line up, mispricings can plausibly last long enough for liquidity provision or contrarian rules to matter—but only if you model not just *average* prices but *who* trades *when* and *under which gates*. The least convenient case for a simple “bias = free money” story is adverse selection and changing regimes; I’ll keep coming back to execution and model risk.

Let’s recap the plan. The note has two layers: **(A)** a compact catalog of major bias families—intuition plus how they tend to show up in markets; **(B)** summary statistics and an entry-threshold sketch for a concrete multi-bot setup (30-day window), to show how strategy design turns abstract bias into **observed trade density and explicit filters** on market-implied probabilities.

---

## 2. Core retail patterns

### 2.1 Favorite–longshot bias

Retail flow often **overweights longshots** (very cheap contracts) and **underweights favorites**. Empirically, low-priced “Yes” legs sometimes resolve positively less often than their prices imply, so the complementary side can earn what practitioners sometimes describe as a **structural premium**—though the size and stability of that premium vary by venue and period. Plausible drivers include:

- **Probability misperception** (Prospect Theory framing): small true probabilities can feel larger; large probabilities can feel smaller than they are.
- **Skew-seeking and lottery demand**: a small stake for a huge payoff attracts flow even when simple expected value is negative.
- **Heterogeneous beliefs and limits to arbitrage**: optimistic flow piles into longshots; capacity and risk limits prevent full compression.

Guards that systematically **fade** extreme longshots—or buy “No” at appropriate prices—are one explicit way people implement an edge *if* they believe this mechanism is priced slowly enough to trade.

### 2.2 Yogi–Berra bias (late-event overoptimism)

Near resolution, prices for the **trailing** side can stay **too high** relative to true comeback odds—the “it ain’t over till it’s over” effect. I think this mixes **overreaction to salient possibilities** (availability), **narrative bias**, and the **emotional pull** of a dramatic reversal. Historical prediction-market and sports work suggests inflation near the end of contests in some settings; late-game or late-window **filters** are designed to lean against that stickiness in longshot prices.

### 2.3 “Nothing ever happens” and base-rate neglect

Many provocative markets—novel scandals, disruptions, one-off events—**resolve negatively** more often than casual intuition suggests. Asking *whether* something dramatic can happen **primes** scenarios for how it *could* occur, which can **inflate** subjective probability while **underusing** the base rate that *most* such propositions do not land “Yes.” Cross-platform patterns are sometimes described as a **pro-Yes** tilt in crowd forecasts; a disciplined **default toward “no”** on specific market classes is one counterweight—again, context-dependent and not universal.

### 2.4 Recency bias and overreaction to recent information

Traders **overweight** the latest news, streaks, or short samples and **underweight** long-run base rates. The **availability heuristic** makes recent vivid outcomes dominate judgment; the **hot-hand** belief (see §3) can amplify this. Fading overreaction—especially after **hype- or shock-driven** moves—pairs naturally with base-rate and longshot–favorite frameworks, if you believe mean reversion is faster than the crowd’s narrative.

### 2.5 Why retail flow can stay exploitable

Several cross-cutting reasons help explain *persistence*, without guaranteeing easy profits:

- **Heuristic, fast** decisions (roughly “System 1”) under stress, entertainment, and short horizons.
- **Selection and incentives**: retail often seeks **action, narrative, and skew**; systematic liquidity can sit on the other side.
- **Structure**: **zero or low fees** can increase volume and churn; **social media** and **thin** books early in a market can extend mispricings.
- **Optimism and wishful thinking** in politicized or identity-relevant events.

None of this makes edge automatic—execution, adverse selection, and model risk still dominate outcomes—but I read these biases less as occasional bugs and more as **features of the demand side** that sometimes leave slow-moving wedges for careful rules.

---

## 3. Hot-hand fallacy

The **hot-hand fallacy** is the belief that success runs in “streaks” beyond what independence or modest true state dependence would justify. Classic basketball work (Gilovich, Vallone, and Tversky) showed that after streaks, **conditional** hit rates were not elevated as much as **fans believed**; later research refined the picture with **selection and difficulty** (harder shots after a streak, and similar complications).

**Betting and markets:** in-play props, team win streaks, and “momentum” narratives can **compress odds** in favor of recent performance. Momentum *can* be real in some environments; the subtler claim is that **belief** in it is often **too strong**—so a systematic **fade of streak-chasing** can pair naturally with recency bias (§2.4) and late-game mispricing (§2.2), always subject to the usual caveats about regime and sport-specific dynamics.

---

## 4. Optimism and pessimism

**Optimism bias** is a tendency to **overestimate** favorable outcomes (and **underestimate** bad ones), often for self-relevant or desirable events. There is serious work suggesting positive information is integrated **more readily** than negative in some settings. In markets, it can appear as **excess “Yes”** on exciting or hopeful resolutions—sometimes described as an **“optimism tax”** on narrative-driven underdogs and feel-good events; the value of that framing should not be overstated outside the domains where it has been tested.

**Pessimism bias** is the **mirror image**: overestimating **bad** outcomes. It is **less universal** but shows up in depressive or high-uncertainty states, or after **strings of losses**. In trading, it can mean **selling** too hard on **bad** headlines or **underpricing** recoveries. For modeling, the key insight is not that **one** sign always dominates but that **affective** and **self-serving** processing can **bend** prices away from **calibration** in **predictable directions** by market type and regime—on balance, a statistical regularity, not a law.

**Interaction with other biases:** optimism stacks with **favorite–longshot** and **halo** (§7); **negativity** (§6) and **Yogi–Berra**-style late fear can create **time-varying** mixes of the two.

---

## 5. Sunk cost fallacy

**Sunk costs** are **irrecoverable** past outlays. Forward-looking choice *should* ignore them; in practice, people **stick with** or **add to** losing propositions to **justify** past spending—**loss aversion** and **consistency** help explain “throwing good money after bad.”

**Markets and prediction contracts:** **loss-chasing**, **refusal to close** bad legs when news worsens, and **doubling down** to “get back” to even can slow **repricing** and keep **stale** beliefs **in open interest**. That can **lengthen** the life of mispricings that **fading** or **LP** strategies care about. Heuristics using **age of OI**, **turnover**, or **stickiness** of volume are indirect ways to probe **sunk-cost-heavy** conditions—noisy signals, but sometimes informative.

---

## 6. Negativity bias

**Negativity bias** is the tendency for **negative** information, affect, and events to **weigh more** than **positive** ones of comparable magnitude—**threats** are often processed **faster** and **remembered** longer. It is **related to** but **distinct from** **loss aversion** and **pessimism** (§4).

**Markets:** after **bad** polls, scandals, or “crisis” frames, **flows into “No”** (or out of “Yes”) can **overshoot**—a **fear** premium. Systematic **contrarian** buying of **depressed** “Yes” (or selling **inflated** “No”) after **spikes** in negative attention is the structural counterpart, with obvious event risk. **Recency** (§2.4) and **sunk** attachment (§5) can **prolong** such dislocations.

---

## 7. Halo and horns

The **halo effect** (Thorndike): a **dominant** positive trait or first impression **spreads** to **unrelated** attributes—for example inferred competence from likability. The **horns** (or **reverse halo**) effect is the **negative** parallel: one bad trait **taints** the whole.

**Markets:** **celebrity**, **branding**, and **charisma** can **bundle** with **Yes** in politics, sports, and entertainment contracts; one **gaffe** can **over-discount** unrelated dimensions. This **interacts** with **recency** (§2.4), **hot-hand** (§3), and **sunk** reluctance to exit (§5). Heuristic guards might **down-weight** or **fade** “Yes” when a **single personality** **dominates** the narrative, or do the **inverse** when a **one-off** **negative** event **depresses** a **structurally** sound “Yes”—highly context-dependent.

---

## 8. Curse of knowledge and hindsight bias

**Curse of knowledge:** once a fact is known, it is hard to **simulate** a **less informed** perspective. **Informed** agents may **overestimate** what **retail** knows, **over-explain** prices, or **misconstrue** **liquidity** needs. For **oracles and designers**, a failure mode is **criteria** and **explanations** that are **obvious** ex post but were **not** in real time for many traders.

**Hindsight bias** (“I knew it all along”): after an outcome, people **reconstruct** beliefs as if it were **foreseeable**, **underestimate** ex-ante **uncertainty**, and **judge** others’ **ex ante** **decisions** harshly. In **prediction markets**, it fuels **disputes**, **overconfidence** in the **next** bet, and **narrative** that **reinforces** some of the same **psychological** **patterns** (e.g., **favorite–longshot**, **halo**).

**Meta point:** these distort **learning**; biased flow can **persist** if **retail** **repeats** **narrative** and **hindsight-consistent** stories. **Modeling** should treat **post-resolution** **sentiment** and **explanations** with care when **tuning** **guards** or **weights**.

---

## 9. Empirical case: execution density and entry gating

In this piece so far, the catalog has stayed mostly qualitative. This section grounds the discussion in **operational** metrics and **gating** rules in the spirit of **Sections 2.2.1–2.2.2** in the accompanying derivation (see also the figure `bias-strategy-figure.png` in this directory). A fixed **observation horizon** $T$ (days), total executed trades $\mathcal{N}(T)$, and **per-minute** activity tie the abstract question—“how often does this strategy act?”—to **concrete** numbers.

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

**Qualitative comparison:** Bonereaper uses the narrowest $q$-window, which is consistent with less dispersion in admissible states and potentially lower per-trade variance on admissible entries (other risks unchanged). `0xB27BC932` admits the widest range of $q$, which widens the opportunity set and can support a higher trade count, often at the cost of higher per-trade variance if the window includes noisier regions of the implied curve. On balance, these tradeoffs echo §§2–8: stricter rules versus action frequency; theory should align with empirical $\bar{\lambda}$ and risk metrics, not replace them.

**Figure (screenshot of the same structure):**  
![Execution rate and entry threshold summary](bias-strategy-figure.png)

---

## 10. Synthesis

Let’s recap. The cognitive catalog (§§2–8) explains why biased prices and order flow are *plausible* in competitive but imperfect markets—without claiming they are always large or stable. The operational block (§9) ties that story to measurable trade frequency and explicit gates on implied levels and edges ($\varepsilon$, $\tau$). Neither layer identifies value on its own; together they constrain how a model of bias maps to what you actually see in trade logs and rule sets.

Open questions remain: which biases dominate which venues and periods, how fast arbitrage and learning erode them, and how much of apparent “bias” is instead informed flow that your model mislabels. I think those empirical forks matter more than adding another named bias to the list—though the list is still a useful map of where to look.

---

## References (conceptual)

- Gilovich, T., Vallone, R., Tversky, A. (1985). *The hot hand in basketball: On the misperception of random sequences.*  
- Kahneman, D., Tversky, A. (1979). *Prospect theory.*  
- Tversky, A., Kahneman, A. (1974). *Judgment under uncertainty: Heuristics and biases.*  
- Fischhoff, B. (1975). *Hindsight ≠ foresight: The effect of outcome knowledge on judgment under uncertainty.*  
- Thorndike, E. L. (1920). *A constant error in psychological ratings.*  
- Sharot, T. (2011). *The optimism bias—work on asymmetry in belief updating.*  

*(This is a working note, not a formal bibliography; cite primary sources for publication.)*
