# Signature Verification Performance and Batch Processing

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion focuses on optimizing signature verification performance for batch processing of deposits in Ethereum. Terencechain proposes a "DC8" strategy that splits batches into 8 sub-batches and recursively processes failed ones down to individual signatures, achieving verification of everything under 2 seconds even with 8 bad signatures. The team is considering raising the batch limit from 8192 to potentially 16384 deposits (corresponding to ~400m gas), though performance testing is needed.

Performance benchmarks reveal significant concerns: verifying 8192 deposits takes 800ms on an M1 Max, with 90% of overhead from pubkey deserialization and subgroup checks, plus cgo overhead when using the blst library. Even with optimizations like parallel pubkey deserialization and avoiding linear scans, the process still takes 500-600ms on high-end hardware, potentially 1-2 seconds on typical mainnet nodes. This creates timing challenges with the proposed 2.5-3s attestation deadline.

The discussion also highlights a potential economic attack vector with deferred payload processing, where builders could strategically include many valid deposits to burden the next proposer and potentially cause them to miss their slot. The team concludes that constraining the number of deposits that consensus blocks must contain may be the only viable solution to address these performance and timing concerns.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T00:16:41.274000+00:00] terencechain: i think the best strategy is DC8...  split the batch into 8 sub batches and recurse into each that fails, down to size 1. Even with 8 bad signatures. i can verify everything under 2s
[2026-05-08T00:18:06.347000+00:00] terencechain: are we going with 8192? or a smaller number? or TBD?
[2026-05-08T00:23:00.321000+00:00] jtraglia: <@363800010518822915> potuz suggested we raise the limit. Might need to test 16384 too. That would correspond to ~400m gas.
[2026-05-08T02:47:50.349000+00:00] terencechain: <@755590043632140352>  <@592004585506340885> whats interesting here is with deferred payload processing, `n+1`'s proposer gets the burden, vs before it's the `n` 's builder gets the burden. That means as a rational builder.. i could include a few valuate mev txs.. then fill rest of my block with builder deposits and try to get `n+1` proposer to miss its slot.. and try to win for `n+2`. Something weird like that..

keep in mind the attack here doesnt have to be invalid deposit. In the happy case.. all valid deposits.. my M1 Max already takes 800ms to batch verify 8192 deposits. 90% of them are pubkeyu deserailziation and subgroup check. I can shred off 100ms by not doing linearly scan of `state.builders` for every `add_builder_from_deposit` call , but that's not enough if we are are thinking to move attestation deadline to 2.5-3s
[2026-05-08T03:16:39.145000+00:00] terencechain: i shred off another 100ms by parallel pubkey deserlization. I dont think prysm can make it faster than 500-600ms to verify 8192 deposits. 99% of the overhead is cgo here 🙁
[2026-05-08T03:17:59.558000+00:00] jtraglia: Which BLS library are you using? blst or a pure-go one? Also, I bet your M1 Max is more powerful than most mainnet nodes.
[2026-05-08T03:21:29.602000+00:00] terencechain: blst.. with the go bindings. 
exactly.. if something takes 500ms on m1 max.. probably takes 1-2s on a med to low end NUC
[2026-05-08T03:22:30.996000+00:00] terencechain: constraint the number of deposits consensus block must have may be the only viable path
```

</details>
