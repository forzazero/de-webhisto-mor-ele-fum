### Paradex Rekt

**Paradex**, a zero-fee perpetual futures DEX built on Starknet (backed by Paradigm), experienced a major technical incident on January 19, 2026.

A database migration/maintenance error caused Bitcoin's price feed to briefly flash to $0 (and affected other assets like PAXG/gold). This triggered widespread incorrect liquidations—thousands of positions, especially longs, were wiped out in seconds, leading to many traders getting "rekt" temporarily.

The team quickly responded by:
- Rolling back the appchain to a prior block (1,604,710).
- Reversing the false liquidations.
- Manually refunding affected users: ~$650k distributed across 200 accounts (mostly PAXG-related).
- Resuming normal operations, including Gigavault deposits/withdrawals.

No users suffered permanent losses—funds were made whole ("SAFU"). The incident highlighted risks in leveraged trading but also showed the platform's rollback capability protected traders. Some criticized the centralized rollback on a "DEX," but many praised the transparent handling and user-first resolution.

Trading volume and XP farming have largely recovered, with ongoing Season 2 rewards boosted 50% in the final weeks.

Paradex's refund process following the January 19, 2026 incident (caused by a database migration error leading to faulty price feeds and incorrect liquidations) was handled proactively by the team, with no user claims required for the primary affected cases.

### Key Details:
- **Chain Rollback First**: The platform rolled back the appchain to block 1,604,710 (a safe state before the glitch), reversing false liquidations and restoring positions as if the error hadn't impacted trading.
- **Proactive Review and Direct Refunds**: The team manually analyzed on-chain data to identify ~200 impacted accounts (mostly tied to PAXG/gold perpetuals, with some BTC and other assets affected). They directly credited refunds totaling approximately $650,000 in USDC or equivalent to these wallets—no applications, forms, or self-reporting needed from users.
- **Timeline**: Refunds were processed and completed within ~24-48 hours after the incident. By January 20, the team announced completion, with Gigavault deposits/withdrawals also resumed.
- **Support Tickets**: Any open tickets specifically related to these refunds were automatically closed. Other unrelated tickets were reviewed separately in the following days.
- **No Permanent Losses**: All users were made whole ("funds SAFU"), with zero actual user losses from the glitch.

This approach was praised in community discussions for its transparency, speed, and user-first execution, contrasting with some past incidents on other platforms that required manual claims.

A full post-mortem was promised but not yet detailed in available updates as of January 22. Trading has fully recovered, with boosted Season 2 XP rewards as additional goodwill.

Does it leaving the bad reputation to the Starknet?
No, the Paradex incident on January 19, 2026, has not left a significant bad reputation on Starknet.

### Why the Impact Is Minimal
- **Isolated to Paradex's Appchain**: Paradex operates as a custom appchain using the Starknet stack (not on the main Starknet L2). The database migration error and subsequent rollback were entirely handled at the Paradex level, with zero direct impact on Starknet's core network, consensus, or other protocols.
- **Community and Insider Clarifications**: Starknet ecosystem leaders (e.g., StarkWare CTO and other devs) quickly emphasized that this was unrelated to Starknet itself. Discussions on X highlight that the rollback reflects Paradex's more centralized operational control (as an appchain), not a flaw in Starknet's decentralized design.
- **Positive Handling Praised**: The quick rollback protected users (no permanent losses, proactive refunds of ~$650k), which many viewed as responsible rather than damaging. This contrasted with past incidents on other platforms and was seen as a strength of appchain flexibility.

### Minor Criticisms
Some raised concerns about "centralization risks" in appchains allowing rollbacks, sparking brief debates on DeFi trust. These were mostly directed at Paradex, not Starknet broadly. Media sometimes loosely called it a "Starknet DEX glitch," but detailed reports and community corrections limited any spillover.

By January 21–22, discussions shifted back to normal trading activity on Paradex and broader Starknet growth (e.g., BTCFi, perps volume), with no sustained negative sentiment toward Starknet. Overall, it's viewed as an app-level operational hiccup in early DeFi infrastructure, not a stain on the underlying L2.