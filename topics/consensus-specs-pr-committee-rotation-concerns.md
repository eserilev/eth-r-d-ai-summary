# Consensus specs PR committee rotation concerns

---

## 2026-03-19 (epbs)

nflaig raised concerns about a consensus specs pull request (#5020) regarding committee rotation. They criticized the current approach as being neither comprehensive enough to allow clients to remove their external PTC (presumably Proof of Time Committee) cache, nor minimal enough to efficiently reduce state size and updates. The main issue highlighted is that the proposal rotates committees every slot just to handle epoch boundary cases, which results in unnecessary rotations 31 times per epoch when only one rotation per epoch would be needed.

The discussion appears to be ongoing with no resolution mentioned, and the technical trade-offs between different committee rotation strategies remain an open question requiring further consideration.

**Participants:** nflaig

<details>
<summary>Raw messages</summary>

```
[2026-03-19T12:35:48.213000+00:00] nflaig: https://github.com/ethereum/consensus-specs/pull/5020 I don't really like the current spec PR as it's neither sufficient to allow clients to remove their external ptc cache while at the same time it's not the minimal approach we can use to reduce state size and updates, rotating committees each slot just to handle the epoch boundary case means we do it 31 times for no reason
```

</details>

