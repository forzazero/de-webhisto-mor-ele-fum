# What is MiroClaw

**MiroClaw** is a *restorage* of the **ZeroClaw** project: a **lightweight, local-first personal AI assistant** implemented **entirely in Rust**. It turns your own hardware into a private, always-on agent you control—reachable from the chat apps and workflows you already use, without paying the resource or privacy tax of heavy cloud stacks or bloated runtimes.

*Mainline code lives in **zeroclaw**; **miroclaw** is a restorage/mirror. The **morpheum-labs** line is actively developed (including into 2026).*

---

## What

MiroClaw is a **single control plane**—a small **Gateway** process plus CLI and optional **web dashboard**—that **connects** a model of your choice to **the channels you use** and to a **large tool surface** (built-in + MCP), with **memory and workspace** on disk under your account.

**In one line:** one **native binary** that orchestrates modern LLMs, **70+ built-in tools**, **20+ chat channels**, and **optional multi-agent “Hands”**—with **low overhead**, **strong sandboxing**, and **local-first data** unless you explicitly use a cloud LLM.

---

## Pain points (what it solves)

| Pain | How MiroClaw addresses it |
|------|---------------------------|
| **Privacy and control** — You do not want your workspace, memory, and habits to default to an opaque third-party *only*. | **Local-first** data: personality, skills, and **persistent memory** live under **`~/.zeroclaw/workspace/`**; **you** decide when a cloud **LLM** is used. |
| **“Assistant” resource tax** — Many stacks add fat runtimes, extra processes, or high idle RAM, so a 24/7 agent on a home box or SBC is unrealistic. | A **100% Rust** **control plane**: **one** **Gateway** process, targets on the order of **< 5 MB** RAM and **< 10 ms** cold start for the core, **no** **bundled** Python/Node for the **core**—suitable for **always-on** modest hardware. |
| **Model and vendor lock-in** — Tying the workflow to one app or one API is brittle when pricing, ToS, or model quality change. | **LLM-agnostic** design (**50+** providers), **swappable** backends via **traits**, and **per-Hand** or **per-task** model choice. |
| **Chat-only “AI”** — A product that can only *talk* does not *do* your work: files, tools, schedule, and integrations stay separate. | **70+** **built-in tools** plus **MCP**; **cron** and **events**; **Hands** for **multi-step** and **autonomous** flows; **hardware** hooks where you need them—not a **single** chat window in isolation. |
| **Context switching** — Jumping between Slack, email, Telegram, and a separate “AI” tab to get one thing done wastes time. | **One agent**, **many surfaces**: **20+** **channels** **plus** **CLI** and **web dashboard**—the same **memory** and **tools** everywhere. |
| **Fear of unconstrained tools** — Shell, browser, and file access *feel* dangerous if there is no clear policy, pairing, or isolation. | **Autonomy** tiers (**ReadOnly → Supervised (default) → Full**), **DM pairing**, **workspace isolation**, **Linux**-style **sandboxing** (e.g. **Landlock / Bubblewrap**), allowlists, and **rate** limits. |

**In short:** a **resident**, **capable** agent (memory, tools, automation, **optional** **swarms**) that still treats **your data**, **model choice**, and **execution policy** as **yours**—**not** a vague “trust us” **black box**.

---

## Why

**Why the architecture (efficiency as a first-class goal)**  
The pain above → **one** small **control plane** that is cheap to run **all day**, **one** process for channels + loop + tools + **schedule**, and **data** on **disk** you can **backup** and **inspect**. The choices show up in the targets below.

MiroClaw is aimed at home servers, SBCs, and 24/7 laptops—places where **watts and MB matter**.

| Dimension | What it means in practice |
|-----------|---------------------------|
| **Memory** | On the order of **< 5 MB RAM** for the core in typical always-on use—avoiding a second fat runtime beside your model. |
| **Startup** | **< 10 ms** cold start for the core—fits systemd, launchd, and edge-style deployments. |
| **Hardware floor** | Reported to run on **~$10-class boards** with a suitable local/remote model setup. |
| **Runtime** | **100% Rust** for the control plane: **no** Python/Node **bundled** for the core—**smaller** attack surface and **less** dependency sprawl. |
| **Process model** | **One** **Gateway** handles WebSockets/HTTP/SSE, channels, the agent loop, tools, scheduling, and the **React 19 + Vite** dashboard. **Fewer processes** means less RAM, less IPC, and fewer moving parts. |

**Local-first by default; cloud LLMs are optional**—you choose when API traffic leaves your network.

**Positioning:** for people who want **OpenClaw-style** “your agent, your chats, real automation” **without** a heavyweight stack or a vague privacy story. The product thesis: **high capability per watt, per MB, and per line of trust**—**deploy once**, then **extend with MCP and Hands** and **tighten sandboxes and autonomy** as you grow.

---

## How

**1. The Gateway (control plane)**  
Running `zeroclaw gateway` starts a small Rust **server** that is the **single hub** for:

- WebSocket / HTTP / **SSE**  
- **20+** messaging integrations (Telegram, WhatsApp, Discord, Slack, email, Signal, iMessage, Matrix, Bluesky, and more)  
- The **agent orchestration loop** and **tool** execution  
- **Scheduled** tasks and **event** hooks  
- Optional **real-time web UI** (dashboard)

**2. The agent loop (per message or job)**  
Same core path for chat, CLI, SOPs, **cron**, and **Hands**:

1. **Ingress** — message (or job) from a channel, CLI, or UI.  
2. **Classification** — intent.  
3. **Memory** — load relevant context from the workspace.  
4. **Prompt** — `IDENTITY.md` / `SOUL.md` and other markdown, SOPs, available tools, conversation state.  
5. **LLM** — your chosen provider.  
6. **Act** — optional **tool** calls, constrained by **sandbox** and allowlists.  
7. **Reply** — back through the same channel, or **hand off** to another **Hand** where configured.

**Hands, cron, and** **`zeroclaw daemon`** reuse this loop. **MCP** tools are discovered and exposed like native tools in that loop.

**3. Modularity (Rust traits)**  
Swap implementations without forking the project:

- **LLM** providers  
- **Channels** (chat apps)  
- **Tools** and **MCP** clients  
- **Memory** backends  
- **Tunnels** (ngrok, Cloudflare, Tailscale, OpenVPN, etc.)

**4. “Hands” (multi-agent layer)**  
**Hands** are **specialized** agents (e.g. Research, Write, Audit). The Gateway **orchestrates** **parallel** and **serial** work via **lanes/queues**, **handoff contracts**, **per-Hand** model and tools, and **ReadOnly → Supervised (default) → Full** **autonomy**. Triggers: **cron**, **webhooks**, **MQTT**, **peripherals**, **SOPs**, **daemon** mode.

**5. Safety in depth**  
**Pairing** for DMs, **workspace isolation** (e.g. sensitive paths), **Landlock / Bubblewrap**-class **sandboxing** on supported OSes, allowlists, rate limits—**dangerous** actions are **constrained by design**.

---

## What to produce

MiroClaw is not only “an answer in chat.” You **produce** and **stage** work in your **workspace** and in **connected** systems. Typical **outputs**:

| Outcome | Examples |
|--------|----------|
| **Conversational** | Replies in Telegram/WhatsApp/Slack/…; same agent from **CLI** or **dashboard**. |
| **File & repo artifacts** | Summaries, drafts, and structured notes written under **your workspace**; **git**-aware workflows. |
| **Scheduled** | **Cron**-driven briefings, log audits, or research dumps (e.g. `zeroclaw cron` with full **agent** prompts, not raw shell only). |
| **Event-driven** | Reactions to **webhooks**, **MQTT**, or **hardware** / **SOP** triggers—e.g. “on new PR, run review Hand.” |
| **Multi-step** | **Hand** chains: **Researcher → Writer → Reviewer** with **handoff** contracts and **persistent** memory so quality **compounds** across runs. |
| **Tool-assisted** | Browser, search, Jira, Notion, Google Workspace, **MCP** servers—whatever you enable—acting under your **autonomy** and **sandbox** policy. |
| **Physical** | **Peripheral**-style control (ESP32, STM32, Arduino, Raspberry Pi-class devices) for **bridging** the agent to **real** hardware. |

**Net:** a **long-lived** assistant that can **author**, **transform**, and **act** in your environment—not just stream tokens.

---

## Features

- **Single native binary** — core control plane in **Rust**; one **Gateway** for channels, loop, tools, and scheduling.  
- **20+** chat and messaging **channels** (and growing).  
- **50+** **LLM** provider integrations; **per-Hand** or per-task model choice.  
- **70+** **built-in tools**: shell, browser, git, files, issue trackers, Notion, Google Workspace, search, and more.  
- **MCP (Model Context Protocol)** — stdio and similar transports; **automatic** tool **discovery** for the same loop as chat and **Hands** (e.g. optional [daily-news MCP](https://github.com/6551Team/daily-news) for `get_news_categories` / `get_hot_news` via TOML config in `~/.zeroclaw/config.toml`).  
- **SOPs** (Standard Operating Procedures) — encode repeatable playbooks.  
- **Cron** and **event** automation; **`zeroclaw daemon`** for **24/7** background operation.  
- **Hands** — **multi-agent** **swarms**, **handoffs**, **parallel** lanes, **graduated** autonomy.  
- **Memory & workspace** — personality and skills in markdown, **persistent** context under `~/.zeroclaw/workspace/`.  
- **Security** — **pairing** for DMs, **ReadOnly / Supervised / Full**, **sandboxing** and **allowlists**, **workspace** isolation.  
- **Tunnels** — optional exposure via **ngrok**, **Cloudflare**, **Tailscale**, **OpenVPN**, etc.  
- **Interfaces** — `zeroclaw agent`, interactive **CLI**, **web dashboard** (real-time chat, memory, config, logs).  
- **Hardware** — **Peripheral** **trait** for custom firmware and device bridges.

**Targets (as stated by the project):** about **< 5 MB** RAM and **< 10 ms** cold start for the core, **< ~$10-class** boards where applicable.

---

## Making the best of MiroClaw (ZeroClaw)

MiroClaw is a capable local agent **runtime** when you **configure** it on purpose. Onboarding, the **workspace** markdown files, **Hands**, **SOPs**, and **automation** matter as much as the model you pick.

### 1. Onboarding (start here, and do not rush)

```bash
zeroclaw onboard
```

This guided flow creates **`~/.zeroclaw/workspace/`**, sets an **LLM** provider, wires **at least one** messaging **channel**, and applies **basic security**. Rushed onboarding shows up in every later session.

After it finishes, verify the install:

```bash
zeroclaw doctor
zeroclaw status
```

**Models:** for general daily use, many people get the best results from a **strong** frontier-class model in their **provider** of choice (for example current **Claude**, **Gemini 2.5**-class, or top **OpenAI** options—pick what you can afford and run under your policy). For **privacy** or **air-gapped** work, use **Ollama** (or another local stack) and size **RAM** to the model; tradeoffs are real.

### 2. The workspace files (where quality is decided)

| File / path | Role | Best practice |
|-------------|------|----------------|
| `IDENTITY.md` | Persona: role, tone, boundaries | Be **specific**; a vague one-liner caps output quality. |
| `SOUL.md` | Values, priorities, how to decide when instructions conflict | Treated as **load-bearing**; bad `SOUL.md` → bland or inconsistent behavior. |
| `USER.md` | **You**—goals, schedule, tools, preferences | **Update** it when your world changes. |
| `AGENTS.md` | Definitions for **Hands** and multi-agent routing | Necessary when you outgrow a single “do everything” persona. |
| `MEMORY.md` (naming can vary) | Durable **facts** the agent should **reuse** | Let the agent maintain it, but **review** occasionally for drift. |
| `SOPs/` | **Standard Operating Procedures**—repeatable playbooks | The main **automation** layer alongside **cron** and **events**. |

**Rule of thumb:** spend about **30–60 minutes** on a solid `IDENTITY.md` and `SOUL.md` before you chase better models or more MCPs—**weak persona files** limit quality faster than a larger **context** window will fix.

### 3. Power-user shape: one brain + **Hands**

- **One** main “daily driver” in chat, **CLI**, and the **web dashboard** when you need to debug.  
- **Specialized** **Hands** (name them in `AGENTS.md` and **route** via SOPs or handoffs), for example: **Researcher**, **Writer**, **Analyst**, **Executor** (heavy tools and shell under **tight** allowlists), and an optional **News** Hand if you add the [daily-news MCP](https://github.com/6551Team/daily-news) (see the appendix).  
- **SOPs** for every **workflow** you run **at least** weekly.  
- **Cron** and, where it helps, **webhooks** and **other** event hooks for work that should run without you in the loop.

**Hands** + **SOPs** + **cron** are what separate this from “another **chat** tab in the browser.”

### 4. High-value use cases

| Use case | How to set it up | Why it works here |
|----------|------------------|-------------------|
| **Daily** news and **X**-style **trend** briefs | [daily-news MCP](https://github.com/6551Team/daily-news) + a **cron** job with an **`--agent`** **prompt** that calls `get_hot_news` and **posts** to a channel | Trending signal without **X** **API** keys; reusable in **Hands** |
| **Personal** chief of staff | Several channels + a solid `USER.md` + SOPs for your routines | One **memory** and tool surface across **Slack**, **Telegram**, **email**-style inboxes, etc. |
| **Research → write** | SOP or Hand **chain** (e.g. Researcher hands off to Writer) | Produces **structured** **artifacts** in the workspace, not a single long chat log |
| **Hardware** | `Peripheral`-class integrations + **your** firmware, documented in SOPs | Natural **language** → actuators, under **governed** **tool** policy |
| **Autonomous** **monitoring** | Cron + webhooks + small SOPs, **ReadOnly** or **Supervised** at first | **24/7** log, sensor, or price **checks** without a constant manual trigger |
| **Codebase assistant** | **Git** + file + **browser** tools; local or policy-approved models for sensitive code | Reduces “paste in chat” risk for teams with strict inference rules |

### 5. Practical levers (best return on time)

**Tools**  
Rely on the **70+** **built-in** **tools** first. Add **MCP** for narrow APIs (daily-news, Composio, custom browser flows, and so on). Use **markdown**-based skills in the **workspace** for **repeat** patterns that do not need a new server for each tweak.

**Automation** (example: 8:00 daily brief; align the **cron** string and `zeroclaw cron` **flags** with your installed CLI):

```bash
zeroclaw cron add "0 8 * * *" --agent \
  "Use get_hot_news for the AI category, summarize the top stories and trending posts, then send the summary to the configured default channel"
```

**Security**  
Start at **Supervised** autonomy, use allowlists for shell and file paths, turn on **sandboxing** (especially for shell and file) where the OS supports it, and use **DM** **pairing** on high-trust inboxes. Move toward **wider** autonomy only when a SOP and a **runbook** have been stable in practice.

**Uptime and performance**  
The control plane is built to be **light**; for always-on behavior run **`zeroclaw gateway`**, or **`zeroclaw daemon`**, or a **systemd** unit—whatever your install documents. If you add **many** MCP servers, use **deferred** MCP loading (if your build offers it) so **gateway** **startup** stays **snappy**.

### 6. A simple learning path

1. **Week 1** — Stable **onboard**, add **2–3** **channels**, write a credible `SOUL.md` and `IDENTITY.md`.  
2. **Week 2** — **1–2** **MCP** **servers** and the first **SOP** you will actually **reuse** more than **once a week**.  
3. **Week 3** — `AGENTS.md`, the first **Hand**, and a simple **handoff** (synchronous is fine to learn the wiring).  
4. **Week 4** — One **boring** **repeating** **task** on a **cron**-only loop; then tune **alerts** and **security** after you have watched it for a week.

### 7. What heavy users do consistently

- **Think in** a **team** (main + **Hands**), not a single all-knowing bot.  
- **Refresh** `USER.md` and long-term memory as goals change.  
- **Use the web dashboard** for logs, memory, and quick **experiments** instead of guessing from chat alone.  
- When MCP does not cover an integration, **bridge** with **n8n**, **Make.com**, or a small **webhook** you own—MiroClaw can own **reasoning** and **orchestration**; external tools can own oddball connectors.  
- Prefer a **dedicated** **always-on** **box** (old laptop, mini **PC**, edge board) for **reliability** and **local** **workspace** **disk** over a phone-only workflow.

**Command checklist** (repeat whenever you change providers, channels, or MCPs):

```bash
zeroclaw onboard
zeroclaw doctor
zeroclaw status
zeroclaw gateway   # or daemon / systemd, per your install
```

---

## Quick start (minimal)

```bash
# Install
brew install zeroclaw   # or project install script

zeroclaw onboard
zeroclaw doctor
zeroclaw status

# Run
zeroclaw gateway

# CLI
zeroclaw agent -m "Summarize my last 10 emails"
```

For the best long-term results, work through **Making the best of MiroClaw** in this file—onboarding, workspace files, **Hands**, and SOPs are what let you use the full ceiling of the stack, not just the first-run defaults.

---

## daily-news MCP (appendix)

1. Clone [6551Team/daily-news](https://github.com/6551Team/daily-news), run `uv sync`, then e.g. `uv run daily-news-mcp` for a local smoke test.  
2. Add a `stdio` `[[mcp.servers]]` entry in `~/.zeroclaw/config.toml` (point `command` / `args` at your `uv` / project path; optional env such as `DAILY_NEWS_MAX_ROWS`).  
3. Restart the gateway. Tools `get_news_categories` and `get_hot_news` then appear in chat, SOPs, and **Hands**—the same wiring pattern works for other MCP servers; for daily-news, you do not need an X API key in the agent itself.

See *High-value use cases* and the 8:00 **cron** example under *Practical levers* above.
