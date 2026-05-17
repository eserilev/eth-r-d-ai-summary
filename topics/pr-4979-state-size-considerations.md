# PR 4979 discussion and state size considerations

---

## 2026-03-20 (epbs)

The discussion centers around PR #4979, which was initially closed but later reopened by jih2nn. The PR is one of three options being considered (#4979, #4992, #5020) for handling some aspect of Ethereum consensus that involves state size trade-offs.

Initially, potuz strongly favored PR #4979 despite adding a few KB to the already several hundred MB state, citing benefits like safer caching, better hashing, easier implementation, and improved RPC provider UX for early duty reporting. Tersec agreed that small state size increases aren't problematic given the current state size, and noted that enshrining caches in consensus provides better testing across the ecosystem. However, potuz later reconsidered after realizing the cumulative impact on RPC providers - while full state clients aren't affected, those using state diffs would see an additional 256KB per checkpoint since the data changes across epochs.

The discussion reveals tension between implementation simplicity and state bloat concerns. While there are legitimate use cases (like simplified slot 0 PTC duty handling and trivial cache management where finalization allows always using head state regardless of branch), some participants like tbenr question adding state data without clear use cases. The conversation ends with three PRs still under consideration and no final consensus reached.

**Participants:** jih2nn, nflaig, potuz, tbenr, tersec

<details>
<summary>Raw messages</summary>

```
[2026-03-20T13:19:51.951000+00:00] potuz: Why was https://github.com/ethereum/consensus-specs/pull/4979 closed? among the three options, this is my favorite, and in fact I'd add 3 epochs to the state
[2026-03-20T13:21:36.592000+00:00] potuz: The cost is just a few KB on a state that is already several hundred MB and the wins are 
- Safer caching
- Better hashing
- Easier implementation
- Better RPC providers UX (report duties early, etc)
[2026-03-20T13:50:48.276000+00:00] tersec: will say given the size of state already, I tend to agree that having things in state tends to be nicer than ad-hoc caches. it also adds better testing because it enshrines the "caches" as part of consensus, the consensus spec tests, etc across the entire ecosystem
[2026-03-20T13:51:38.114000+00:00] tersec: so there might be other arguments against the two variations which changes state for this purpose, but a small increase in state size isn't really one of them for me
[2026-03-20T14:02:36.643000+00:00] jih2nn: Just reopened #4979. We can close PRs once we reach to consensus.
[2026-03-20T14:02:42.830000+00:00] jih2nn: To sum up, currently what's on the table are:

- [#4979](<https://github.com/ethereum/consensus-specs/pull/4979>)
- [#4992](<https://github.com/ethereum/consensus-specs/pull/4992>)
- [#5020](<https://github.com/ethereum/consensus-specs/pull/5020>)
- or yet another approach
[2026-03-20T14:17:07.098000+00:00] potuz: I've gradually changed my mind about the extra size on the state. Although it's cumulative on RPC providers cause these will be guaranteed to be different across epochs.
[2026-03-20T15:36:55.633000+00:00] tbenr: > Although it's cumulative on RPC providers cause these will be guaranteed to be different across epochs.

what do you mean?
[2026-03-20T15:38:09.007000+00:00] potuz: For clients that save full states it's irrelevant. For clients that save state diffs it'll be really 256KB more per checkpoint
[2026-03-20T15:41:54.851000+00:00] tbenr: yes i see. I'm also not a fan of writing data in state when there are 0 usecases attached to it, even if I get the aesthetics of having it simila ro the proposer_lookahead
[2026-03-20T15:42:57.548000+00:00] nflaig: there is a use case for lookahead, makes slot 0 ptc duty handling ~~much~~ slightly simpler
[2026-03-20T17:09:41.468000+00:00] potuz: it also makes cache handling trivial: if we're finalizing we always have dependent root being equal as the head state so we can *always* use head regardless of branch
[2026-03-20T17:10:04.080000+00:00] potuz: this has been a relief for us since Fulu
```

</details>

