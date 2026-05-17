# Gas limit issues with BatchDeposit contract deployment

**Channel:** epbs | **Date:** 2026-05-10

## Summary

terencechain encountered a gas limit issue when deploying a BatchDeposit contract on a Geth + Prysm devnet using ethereum-package. When setting the genesis gas limit to 200M or 150M, the contract deployment fails with "contract creation code storage out of gas" using exactly 2M gas, while the same transaction succeeds with ~920k gas when using the default genesis gas limit.

barnabasbusa suggested this issue is related to EIP-8073, explaining that higher gas limits cause batch creation to be priced more dynamically. As a workaround, terencechain found that a 120M genesis gas limit works and allows testing ~4000 deposits per slot with good performance from cache and batch verification.

The issue was escalated to "pk" for further investigation, and terencechain noted interest in testing with invalid signatures in deposits as a next step.

## Participants

- barnabasbusa
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-10T18:06:48.611000+00:00] terencechain: hi <@808969530608451584> / <@412614104222531604> , running into a weird situation in ethereum-package on a gloas devnet (kurtosis, geth + prysm). When I set `network_params.genesis_gaslimit: 200000000` (or 150M), the assertoor builder-deposit-spam playbook fails on the first step, the BatchDeposit contract deploy reverts with `contract creation code storage out of gas", gasUsed = exactly 2,000,000` 
Same identical tx succeeds with ~920k gas when genesis_gaslimit is left at default
[2026-05-10T18:07:39.385000+00:00] barnabasbusa: it might be 8073 related
[2026-05-10T18:07:50.897000+00:00] barnabasbusa: the more gas limit there is, the more dynamically the batch creation is priced
[2026-05-10T18:08:06.225000+00:00] barnabasbusa: I'll let pk look into this one
[2026-05-10T18:40:16.548000+00:00] terencechain: `genesis_gaslimit` 120M works so I can at least test ~4000 deposits per slot.. but anything higher i think we need a higher gas limit.. I could also set regular gas limit higher.. but that will take a while to catch up
[2026-05-10T18:43:35.128000+00:00] terencechain: 4000 deposits look good with our cache and batch verification, will be cool to have some invalid sigs in the deposits
```

</details>
