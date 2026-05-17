# 32 ETH requirement discussion and attack economics

---

## 2026-05-08 (epbs)

The discussion centers around a proposed 32 ETH requirement as a defense mechanism against certain attacks on Ethereum. While nflaig questions the effectiveness of this approach, arguing it only increases attack costs rather than preventing them entirely and remains affordable for well-resourced actors like nation states, potuz provides context that the underlying attack isn't severely disruptive—it may cause beacon block reorganizations but doesn't stall the chain, and similar liveness failures can be achieved more cheaply through other means like buying payload slots without delivery.

Potuz acknowledges that Misha's pull request appears to work technically but expresses concern about the timing of implementing such changes. He suggests it would be preferable to address these issues through gas limit adjustments only, rather than introducing the 32 ETH requirement mechanism at this point in development.

The conversation leaves open questions about the minimum viable size and financing requirements for builders, as well as the broader question of whether to proceed with the 32 ETH requirement or pursue alternative approaches through existing gas limit controls.

**Participants:** nflaig, potuz, tersec

<details>
<summary>Raw messages</summary>

```
[2026-05-08T11:58:25.799000+00:00] tersec: how small can a builder be an be well-enough-financed to be useful?
[2026-05-08T11:59:51.658000+00:00] nflaig: I don't understand how making it 32ETH solves anything, it just makes the attack more expensive, not impossible, still cheap for a nation state or similar
[2026-05-08T12:05:58.261000+00:00] potuz: the attack is not that problematic, is not that it stalls the chain, it just may reorg some beacon blocks. You can achieve that sort of liveness failure cheaper by simply buying the payload slot and not delivering. By making this more expensive we detract attackers from even exercising this
[2026-05-08T12:06:54.551000+00:00] potuz: At any rate, Misha's PR *seems* to work, but I still think it' s a bad idea to start making these changes now, it would be much nicer if we could handle these with the gas limit only
```

</details>

