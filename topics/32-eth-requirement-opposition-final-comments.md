# 32 ETH Requirement Opposition and Final Comments

---

## 2026-05-08 (epbs)

The discussion centers around opposition to a proposed 32 ETH requirement, with the main concern being that it would price out smaller builders from participation. While one participant questioned how useful very small builders can realistically be, another argued that the 32 ETH threshold merely makes attacks more expensive rather than preventing them entirely, noting that nation-state level attackers would still find it affordable.

The conversation reveals that the attack in question doesn't halt the chain but can cause beacon block reorganizations, with potuz noting that similar liveness failures can be achieved more cheaply by purchasing payload slots and failing to deliver. The higher barrier is intended as a deterrent rather than absolute protection. There's reference to "Misha's PR" as a potential solution, though potuz expresses preference for handling these issues through gas limit adjustments rather than implementing new changes at this time.

**Participants:** 0xunclebill, m.kalinin, nflaig, potuz, tersec

<details>
<summary>Raw messages</summary>

```
[2026-05-08T11:47:42.254000+00:00] m.kalinin: i believe you tried to argue for that hard, didn't you? what was the main opposition?
[2026-05-08T11:49:41.185000+00:00] 0xunclebill: i think the argument was that 32eth prices out the smaller builders
[2026-05-08T11:58:25.799000+00:00] tersec: how small can a builder be an be well-enough-financed to be useful?
[2026-05-08T11:59:51.658000+00:00] nflaig: I don't understand how making it 32ETH solves anything, it just makes the attack more expensive, not impossible, still cheap for a nation state or similar
[2026-05-08T12:05:58.261000+00:00] potuz: the attack is not that problematic, is not that it stalls the chain, it just may reorg some beacon blocks. You can achieve that sort of liveness failure cheaper by simply buying the payload slot and not delivering. By making this more expensive we detract attackers from even exercising this
[2026-05-08T12:06:54.551000+00:00] potuz: At any rate, Misha's PR *seems* to work, but I still think it' s a bad idea to start making these changes now, it would be much nicer if we could handle these with the gas limit only
```

</details>

