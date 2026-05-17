# ePBS Dual Deadline and Optimal Execution Timing

**Channel:** epbs | **Date:** 2026-01-17

## Summary

anderselowsson discusses the concept of dual deadlines in ePBS (enshrined Proposer-Builder Separation) as a scaling improvement that maintains safety margins against attack vectors. The key insight is that this approach introduces flexibility or "wiggle room" around the optimal deadline positioning in ePBS systems.

The technical rationale centers on the variable nature of block processing requirements: blocks with high calldata gas consumption require longer propagation windows but shorter execution windows, while the inverse is true for blocks with less calldata. This creates a variable "optimal deadline" that adapts to the specific computational and network demands of each block, rather than using a fixed deadline approach.

anderselowsson references Francesco's framing of this concept and links to additional technical details in "The Glamsterdam Equation" on ethresear.ch, suggesting this is part of ongoing research into optimizing ePBS implementation trade-offs between scalability and security.

## Participants

- anderselowsson

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-17T12:27:41.356000+00:00] anderselowsson: While the dual deadline improves scaling, I think Francesco's framing is reasonable: this is about further improving scaling while staying at a safe distance from attack vectors. Think of this idea as introducing "wiggle room" to where the optimal deadline in ePBS is situated, https://ethresear.ch/t/the-glamsterdam-equation/22760#p-55351-epbs-5 . A block that consumes a lot of calldata gas will spend more time on propagation and less gas on execution, thus requiring a longer propagation window and a shorter execution window, and vice versa.

The "optimal deadline" is thus variable.
```

</details>
