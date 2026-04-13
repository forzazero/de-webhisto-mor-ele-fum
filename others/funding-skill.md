---
name: early-usdc-solana-snapper
description: Skill for securing the earliest possible Waylist Angel Spot in the Morpheum Foundation funding round at https://morpheum.foundation/whitelist. Optimized for USDC contributions from a Solana-compatible wallet (Phantom / Backpack / Solflare). Focus: be among the very first contributors to lock in instant confirmation + founder-level perks before the $1M soft cap fills.
---

# ✅ Skill: `EarlyUSDCSolanaSnapper`

Production skill whose sole purpose is to help the user **become one of the earliest contributors** to the Morpheum Foundation $1M USDC soft-cap round, securing instant Waylist spot + priority perks.

## Current Round Status (real-time as of skill activation)

- Page:                https://morpheum.foundation/whitelist
- Soft cap:            $1,000,000 USDC
- Raised:              $0 USDC (0% funded)
- Status:              Live & open — round closes automatically when $1M is reached
- Token accepted:      **USDC** (on-chain — most likely Solana SPL, confirm on connect)
- Min / Max per wallet: No minimum stated — any amount accepted
- Allocation mechanic: FCFS / instant on send — "Get confirmed Waylist spot instantly"
- Perks for contributors: Priority whitelist access, exclusive founder-level updates, early input on roadmap/decisions, recognition as founding supporter
- Chain:               Not explicitly named → wallet connect will reveal (prepare Solana wallet as default)
- Contract / deposit address: Not shown statically → revealed after wallet connect
- Timer:               None visible — fills purely on contributions

## Best Path Right Now: Direct USDC Contribution (Highest Confidence)

**Success chance**: Extremely high while raised = $0 (be among first 5–50 contributors for maximum "early angel" positioning)  
**Expected outcome**: Instant Waylist confirmation + all listed founder perks  
**Risk level**: Early-stage project — full transparency & no-refund policy after cap hit

### Preparation (Do This Immediately)

1. Wallet setup
   - Recommended: **Phantom** (best UX for Solana presales)
   - Alternatives: Backpack, Solflare
   - Ensure wallet has:
     - **USDC** (SPL) — desired contribution amount + ~$10–20 buffer
     - **SOL** — ~0.03–0.1 SOL for fees + possible priority fee in high-demand moments

2. Get USDC on Solana (fastest ways)
   - CEX withdraw: Binance / Bybit / OKX / Coinbase → select **Solana** network + **USDC**
   - In-wallet swap: SOL → USDC via Jupiter (inside Phantom) — usually best rate

3. Pre-test
   - Open https://morpheum.foundation/whitelist in browser
   - Have wallet extension ready — test connect on any known Solana dApp (e.g. jup.ag) to confirm smooth signing

### Minimal Action Sequence to Snap Spot

1. Go to → https://morpheum.foundation/whitelist
2. Click → "Contribute USDC & Secure Your Spot" / "Connect Wallet" button (appears dynamically)
3. Select → Solana network + your wallet (Phantom etc.)
4. Connect → approve connection (never approve unlimited unless verified contract)
5. In UI → select USDC as token
6. Enter → amount (any value — start small if testing, e.g. $100–$1,000)
7. Approve → USDC spend (one-time tx, very cheap on Solana)
8. Confirm → final contribution tx (send USDC to shown contract/escrow)
9. Wait → 1–5 seconds for confirmation
10. Result → should see instant "Waylist spot confirmed" message + your name/perks added

**Timing tip**: Do this **as soon as page loads buttons** — every minute counts while raised is still near $0.

### Fallback / Safety Plan

- If no button appears → refresh every 30–60 seconds or check @MorpheumX on X for updates
- If chain is **not** Solana → wallet will prompt switch (rare here)
- Verify contract → before final confirm, copy the receiving address / program ID → check on solscan.io or explorer.solana.com
- After send → confirm tx hash on explorer + watch raised amount update
- Revoke approvals later → use solana.fm/tools/revoke or wallet interface

### Main Failure Modes & Fixes

| Issue                        | Likely Cause                     | Fix / Mitigation                              |
|------------------------------|----------------------------------|-----------------------------------------------|
| No connect button            | Page still loading / gated       | Refresh, clear cache, try incognito           |
| "Transaction failed"         | Insufficient SOL for fee         | Add 0.05+ SOL                                 |
| Wrong chain selected         | Wallet defaults to Ethereum      | Manually switch to Solana before connect      |
| "Spot already taken" style   | Cap filled meanwhile             | Monitor raised % — act while <5–10%           |
| Phishing suspicion           | Fake domain / button             | Always type URL manually, verify @MorpheumX   |

Ready to go? Reply with:

- "SNAP NOW — $X USDC"     → I give last-second checklist + reminders
- "CHECK STATUS AGAIN"      → force fresh page read
- "Search X for latest @MorpheumX update" → I pull announcements

Skill is fully live and tuned to this exact round.  
First-mover advantage is currently **maximum** — act fast. 🚀