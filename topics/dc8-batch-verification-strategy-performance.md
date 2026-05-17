# DC8 Batch Verification Strategy and Performance

---

## 2026-05-08 (epbs)

The discussion centers on optimizing batch verification performance for deposits using a "DC8" (divide-and-conquer with 8 sub-batches) strategy. Terencechain reports that this approach can verify batches in under 2 seconds even with 8 bad signatures by recursively splitting failed batches down to individual signatures. The team is considering raising the batch size limit from 8192 to potentially 16384 deposits (corresponding to ~400m gas), though performance testing is needed for the larger size.

Performance benchmarks reveal significant concerns: verifying 8192 deposits takes 800ms on an M1 Max, with 90% of overhead from pubkey deserialization and subgroup checks, and 99% of total overhead from CGO calls to the blst library. Even with optimizations like parallel pubkey deserialization and avoiding linear state scans, the process still takes 500-600ms on high-end hardware, suggesting 1-2 seconds on typical mainnet nodes. This creates potential attack vectors where builders could include many valid deposits to burden the next proposer and cause slot misses.

The discussion concludes that constraining the number of deposits per consensus block may be the only viable solution, especially given plans to move attestation deadlines to 2.5-3 seconds. The performance bottleneck appears fundamental to the BLS library implementation rather than algorithmic inefficiency.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

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

