# ACDC Censored Block Flag Discussion

**Channel:** epbs | **Date:** 2026-01-09

## Summary

nero_eth outlined several concerns about implementing a "censored block" flag in `engine_notifyNewPayload` for ACDC. The main issues include breaking the current deterministic nature of block validation by introducing mempool dependencies, and the lack of standardized mempools across clients leading to false censorship detection. Different clients have varying configurations for transaction acceptance (min tips, pool sizes, nonce handling), which could cause validators to incorrectly blacklist builders when blocks appear censored due to mempool differences rather than actual censorship.

Additional problems include builders having no feedback mechanism to understand why they're blacklisted (potentially losing months of opportunities), and the risk of creating a "combinatorial mess" where different EL/CL client combinations implement varying censorship heuristics and blacklisting behaviors. nero_eth noted this approach might work for FOCIL where subjective views are beneficial, but argued it's unsuitable for builder blacklisting decisions.

potuz responded briefly suggesting a "Boolean for censoring," though no clear consensus or resolution was reached in this exchange. The discussion appears to be ongoing with significant technical concerns about implementation feasibility and unintended consequences remaining unaddressed.

## Participants

- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-09T06:37:39.127000+00:00] nero_eth: Continuing the ACDC discussion on whether ELs should return a “censored block” flag in `engine_notifyNewPayload`, i.e. signal if a block built by someone else appears to censor transactions based on the node’s *local* mempool view, those were the main thoughts I had:

1. Today this flow is deterministic across nodes: the same payload yields the same response. Nothing dynamic like the mempool is involved. Block validation and the mempool are intentionally separated; you can fully validate blocks without even running a mempool.

2. There is no such thing as a “standard” mempool. Clients differ widely in defaults and configs (min tip, max tx size, pool size, tx lifetime, max txs per account, nonce gap handling, etc.). This means a block can *look* censored simply because the builder’s client never accepted a given tx into its mempool. Result: validators might blacklist builders for non-censorship reasons → bad validator UX. Example: Erigon and Reth accept far more txs into their mempool than Geth or Nethermind. Besu uses a min tip of 1000 wei instead of 1 wei like others.
[2026-01-09T06:37:54.515000+00:00] nero_eth: 3. Builders would have no feedback loop. They wouldn’t even know they’re blacklisted until they don't get a registration ~5 months later when the proposer proposes again. In practice, builders loose months of opportunity with no real signal as to why.

4. EL clients would each invent their own heuristics for what they consider “censorship,” and CL clients would then invent their own interpretations of that signal. For example: does the *first* “censored” response already trigger blacklisting at the CL? And if yes, for how long? Once you start pairing every EL with every CL, you get a combinatorial mess: an EL with aggressive heuristics combined with a CL that treats the signal as “hard blacklist” could easily lead to very weird (and fragile) behavior.

5. To distinguish “actually censored” from “couldn’t afford basefee,” EL clients would need to track historical basefees. This might not be the biggest issue.

This kind of local, subjective view makes sense for FOCIL, where diversity of views is a feature (and full txs go into inclusion lists, so they don’t remain local), but not for blacklisting builders.
[2026-01-09T12:38:25.891000+00:00] potuz: Boolean for censoring.
```

</details>
