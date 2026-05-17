# Signature verification optimization and attack vectors

---

## 2026-05-08 (epbs)

The discussion centers on signature verification optimization for deposit requests and associated attack vectors. Terencechain proposes a DC8 strategy (splitting batches into 8 sub-batches and recursing on failures) that can verify signatures under 2 seconds even with 8 bad signatures. The team is considering raising the deposit limit to 8192 or potentially 16384 (corresponding to ~400m gas), but faces a significant attack vector where adversaries can cause network disruption using valid signatures that can later be withdrawn, making the attack essentially free since only capital availability (not spending) is required.

Potuz explains that valid signature attacks are more dangerous than invalid ones because the ETH can be recovered through withdrawal, allowing sustained attacks with sufficient capital (potentially $80m+ for continuous disruption). Even with optimized verification taking 1.23 seconds for mixed valid/invalid signatures, this is enough to cause block reorgs. The attack becomes more complex with deferred payload processing, where builders could potentially grief subsequent proposers by including many deposits to cause them to miss slots.

Performance testing on M1 Max shows 800ms verification time for 8192 deposits, with 90% overhead from pubkey deserialization and subgroup checks. Even with optimizations (parallel processing, avoiding linear scans), verification times remain around 500-600ms on high-end hardware, suggesting 1-2 seconds on typical nodes. The team concludes that constraining the number of deposits per consensus block may be the only viable solution, as the current approach creates unacceptable attack surfaces and performance bottlenecks.

**Participants:** jtraglia, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-08T00:16:41.274000+00:00] terencechain: i think the best strategy is DC8...  split the batch into 8 sub batches and recurse into each that fails, down to size 1. Even with 8 bad signatures. i can verify everything under 2s
[2026-05-08T00:18:06.347000+00:00] terencechain: are we going with 8192? or a smaller number? or TBD?
[2026-05-08T00:23:00.321000+00:00] jtraglia: <@363800010518822915> potuz suggested we raise the limit. Might need to test 16384 too. That would correspond to ~400m gas.
[2026-05-08T00:24:38.802000+00:00] potuz: The biggest problem is that the attack is essentially free. It's just the gas spent unless we act fast to slash them
[2026-05-08T00:25:01.591000+00:00] potuz: You need to have a lot of capital but you recover it
[2026-05-08T00:25:09.717000+00:00] potuz: So any large group can trigger it
[2026-05-08T00:25:48.552000+00:00] jtraglia: Couldn't a malicious builder include deposit requests with invalid signatures for free? Up to whatever the limit is.
[2026-05-08T00:26:01.638000+00:00] jtraglia: So the `AllBad` case might need to be taken serious.
[2026-05-08T00:34:12.623000+00:00] potuz: Invalid signatures is not an attack
[2026-05-08T00:34:16.406000+00:00] potuz: Valid ones are
[2026-05-08T00:34:50.988000+00:00] potuz: I don't really care about someone that can burn 8K ETH per block
[2026-05-08T00:35:18.275000+00:00] potuz: The problem is that you can use 8K ETH and withdraw it in a few epochs
[2026-05-08T00:38:35.112000+00:00] jtraglia: Is this because these would be rejected before the signature checks?
[2026-05-08T00:39:04.932000+00:00] potuz: No, because invalid signature deposits are burned money
[2026-05-08T00:39:11.575000+00:00] potuz: The EL doesn't return it
[2026-05-08T00:39:24.384000+00:00] jtraglia: Either way. $20m really is not _that_ much to a company/attacker/wealthy_person/nation_state.
[2026-05-08T00:39:27.915000+00:00] potuz: Valid ones are the problem
[2026-05-08T00:39:52.620000+00:00] potuz: And what I'm arguing is that you don't need to "spend" 20M
[2026-05-08T00:39:56.490000+00:00] potuz: You just need to have it
[2026-05-08T00:40:02.069000+00:00] potuz: The attack is free
[2026-05-08T00:40:03.606000+00:00] jtraglia: Yes, that's what I'm saying too.
[2026-05-08T00:40:41.222000+00:00] jtraglia: Okay sorry. We agree.
[2026-05-08T00:41:19.799000+00:00] jtraglia: But even then, $20m burned to cause a big network issue? Could be worth it.
[2026-05-08T00:41:43.767000+00:00] jtraglia: Would this just cause a few missed slots or worse?
[2026-05-08T00:42:15.235000+00:00] jtraglia: Say it takes 20 seconds to verify 8k invalid signatures.
[2026-05-08T00:45:48.640000+00:00] potuz: It's not burned!
[2026-05-08T00:45:53.281000+00:00] potuz: You recover it
[2026-05-08T00:46:02.043000+00:00] potuz: So you trigger it again
[2026-05-08T00:46:12.981000+00:00] potuz: And if you have 80m you do it constantly
[2026-05-08T00:46:21.228000+00:00] potuz: Every single slot
[2026-05-08T00:46:35.453000+00:00] potuz: Probably need a few more millions but you get the point
[2026-05-08T00:47:03.600000+00:00] potuz: Gas is cheap, cause you don't need to compete for inclusion
[2026-05-08T00:47:20.034000+00:00] jtraglia: Didn't you just say it's burned here?
[2026-05-08T00:47:35.823000+00:00] potuz: Only on invalid ones
[2026-05-08T00:47:42.705000+00:00] potuz: If I attack I'll send valid ones
[2026-05-08T00:47:44.022000+00:00] jtraglia: Verifying invalid signatures is slower. That is what I'm talking about here.
[2026-05-08T00:47:49.123000+00:00] potuz: And withdraw
[2026-05-08T00:48:03.342000+00:00] jtraglia: Yeah I understand that attack too.
[2026-05-08T00:48:12.289000+00:00] potuz: You only need to send one invalid and 8K valid if you want to grief that
[2026-05-08T00:48:31.977000+00:00] jtraglia: But according to <@363800010518822915>, that might only take a few seconds to verify. It's not even an attack.
[2026-05-08T00:48:48.407000+00:00] potuz: More than 2 seconds reorgs the block
[2026-05-08T00:49:02.693000+00:00] jtraglia: 8191 valid + 1 invalid is still just 1.23 seconds.
[2026-05-08T00:49:23.799000+00:00] potuz: That's more than enough to reorg most blocks
[2026-05-08T00:49:40.873000+00:00] potuz: And the problem is that the next block needs to include the same requests
[2026-05-08T00:49:48.854000+00:00] potuz: Forever
[2026-05-08T00:50:54.957000+00:00] jtraglia: Then why are you suggesting we raise the limit?
[2026-05-08T00:52:16.978000+00:00] potuz: Because invalidating the Payload seems to be even worse
[2026-05-08T00:52:53.520000+00:00] potuz: Then there's not even a chance of recovering unless a builder doesn't pack the txs
[2026-05-08T00:53:35.183000+00:00] potuz: With this we only need the next proposer not to reorg the block
[2026-05-08T00:54:00.802000+00:00] potuz: Which I think will happen if we stick to the current honest reorg feature of only reorging one slot
[2026-05-08T00:54:17.643000+00:00] potuz: So the first block will be reorged but the second one won't
[2026-05-08T00:54:40.215000+00:00] potuz: Will take seconds to execute but the third proposer should build on the previous block
[2026-05-08T00:54:48.629000+00:00] potuz: So I think we should eventually heal
[2026-05-08T00:54:59.766000+00:00] potuz: The problem is that the attack can be always continued
[2026-05-08T02:47:50.349000+00:00] terencechain: <@755590043632140352>  <@592004585506340885> whats interesting here is with deferred payload processing, `n+1`'s proposer gets the burden, vs before it's the `n` 's builder gets the burden. That means as a rational builder.. i could include a few valuate mev txs.. then fill rest of my block with builder deposits and try to get `n+1` proposer to miss its slot.. and try to win for `n+2`. Something weird like that..

keep in mind the attack here doesnt have to be invalid deposit. In the happy case.. all valid deposits.. my M1 Max already takes 800ms to batch verify 8192 deposits. 90% of them are pubkeyu deserailziation and subgroup check. I can shred off 100ms by not doing linearly scan of `state.builders` for every `add_builder_from_deposit` call , but that's not enough if we are are thinking to move attestation deadline to 2.5-3s
[2026-05-08T03:16:39.145000+00:00] terencechain: i shred off another 100ms by parallel pubkey deserlization. I dont think prysm can make it faster than 500-600ms to verify 8192 deposits. 99% of the overhead is cgo here 🙁
[2026-05-08T03:17:59.558000+00:00] jtraglia: Which BLS library are you using? blst or a pure-go one? Also, I bet your M1 Max is more powerful than most mainnet nodes.
[2026-05-08T03:21:29.602000+00:00] terencechain: blst.. with the go bindings. 
exactly.. if something takes 500ms on m1 max.. probably takes 1-2s on a med to low end NUC
[2026-05-08T03:22:30.996000+00:00] terencechain: constraint the number of deposits consensus block must have may be the only viable path
```

</details>

