# State Caching and Performance Optimization

**Channel:** epbs | **Date:** 2026-04-14

## Summary

The discussion centers on whether to implement caching for withdrawal computations when applying envelope requests for next block proposals. **tbenr** asks about precomputing withdrawals by applying envelope requests immediately if you're the next proposer, allowing builders to have them ready when deciding to build on the full block.

**potuz** confirms this is the caching approach but expresses concerns that implementing it would make their cache significantly more complicated and bug-prone. He notes that the computation appears fast enough that caching may be unnecessary, which is why he stopped advocating for it. **tbenr** agrees the mutation seems fast and doesn't require state hashing, though mentions they need to evaluate performance in Teku.

**terencechain** raises the concern that state copying isn't free, while **tbenr** clarifies that copying isn't problematic for their implementation, but materializing changes in validators and balances from execution requests could be expensive. The team plans to track performance metrics and evaluate whether the optimization is worthwhile. The discussion ends mid-sentence with **nflaig** beginning to comment on copying costs.

## Participants

- nflaig
- potuz
- tbenr
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-14T14:47:27.647000+00:00] tbenr: <@755590043632140352> is this the caching you were talking about? so you probably want to apply envelop requests immediately to precompute the withdrawals if you are the next proposer so you'll have them already if you decide do build on full?
[2026-04-14T14:48:44.829000+00:00] potuz: Yes but if we do this our cache becomes actually much more complicated and bug prone as I was complaining early on. I think the computation is fast enough that probably we will be fine without caching that's why I stopped complaining
[2026-04-14T14:49:34.371000+00:00] tbenr: yes, it seems to me a fast mutation and we don't need to hash the state neither
[2026-04-14T14:49:46.813000+00:00] tbenr: we need to check on teku 😄
[2026-04-14T15:35:34.429000+00:00] terencechain: i think the main cost here is you need to copy the state and that's not "free"
[2026-04-14T15:42:09.219000+00:00] tbenr: copying isn't an issue for us, but materializing changes in validators and balances carried by execution requests might not be that cheap. We will track the performance and evaluate.
[2026-04-14T16:02:52.271000+00:00] nflaig: copying isn't an issue for us, but
```

</details>
