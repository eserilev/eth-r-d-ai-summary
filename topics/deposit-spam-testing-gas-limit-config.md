# Deposit Spam Testing and Gas Limit Configuration

---

## 2026-05-10 (epbs)

The team was conducting deposit spam testing to evaluate Ethereum's handling of large deposit batches. Initial tests showed that 1000 deposits worked successfully, but attempting 8192 deposits failed due to transaction size limits (1.4MB vs 131KB limit). To handle 8192 deposits, a 200M gas limit was recommended compared to 150M for smaller batches.

However, when terencechain configured higher genesis gas limits (150M-200M) in their ethereum-package devnet setup, the BatchDeposit contract deployment began reverting with "contract creation code storage out of gas" errors, consuming exactly 2M gas. The same deployment succeeded with ~920k gas under default genesis gas limits. barnabasbusa suggested this issue might be related to EIP-8073, where higher gas limits cause dynamic pricing adjustments for batch creation.

As a workaround, terencechain found that a 120M genesis gas limit allowed testing of ~4000 deposits per slot successfully. The team noted that cache and batch verification performed well at this scale, with plans to incorporate invalid signatures in future deposit testing. The underlying gas limit pricing issue was flagged for further investigation.

**Participants:** barnabasbusa, jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-10T16:51:54.332000+00:00] terencechain: 1000 works
[2026-05-10T16:52:02.773000+00:00] terencechain: i tried to go 8192 deposits and got this from assertor: `failed sending batch tx: oversized data: transaction size 1442245, limit 131072`
[2026-05-10T16:52:51.913000+00:00] jtraglia: Nice. Are you using 150m gas limit? Needs 200m for 8192.
[2026-05-10T18:06:48.611000+00:00] terencechain: hi <@808969530608451584> / <@412614104222531604> , running into a weird situation in ethereum-package on a gloas devnet (kurtosis, geth + prysm). When I set `network_params.genesis_gaslimit: 200000000` (or 150M), the assertoor builder-deposit-spam playbook fails on the first step, the BatchDeposit contract deploy reverts with `contract creation code storage out of gas", gasUsed = exactly 2,000,000` 
Same identical tx succeeds with ~920k gas when genesis_gaslimit is left at default
[2026-05-10T18:07:39.385000+00:00] barnabasbusa: it might be 8073 related
[2026-05-10T18:07:50.897000+00:00] barnabasbusa: the more gas limit there is, the more dynamically the batch creation is priced
[2026-05-10T18:08:06.225000+00:00] barnabasbusa: I'll let pk look into this one
[2026-05-10T18:40:16.548000+00:00] terencechain: `genesis_gaslimit` 120M works so I can at least test ~4000 deposits per slot.. but anything higher i think we need a higher gas limit.. I could also set regular gas limit higher.. but that will take a while to catch up
[2026-05-10T18:43:35.128000+00:00] terencechain: 4000 deposits look good with our cache and batch verification, will be cool to have some invalid sigs in the deposits
```

</details>

