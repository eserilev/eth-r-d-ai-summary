# EPBS Network Hardening Test Failures

**Channel:** epbs | **Date:** 2026-03-16

## Summary

Parithosh reported a network hardening test failure in the EPBS (Enshrined Proposer-Builder Separation) implementation. The test involves stopping block production via the snooper API while allowing attestations to continue, then re-enabling block production. While latest releases show the chain healing after some time, the `epbs-devnet-0-sync` branch fails to recover from this scenario.

Potuz inquired about the specifics of the chain splitting and healing behavior, particularly regarding the attestation status during the test. They mentioned a pending Prysm PR that should help address the issue and asked about which versions of Lighthouse and Lodestar are being used, specifically whether they send index=1 attestations when blocks are missing.

The conversation remains open with technical questions about client behavior during block production interruptions. The test can be reproduced using the diamond repository with the `SCENARIO=nfin-001` configuration, though Parithosh's final message appears to contain a typo referencing "nft-001".

## Participants

- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-16T13:53:17.708000+00:00] parithosh: <@755590043632140352> i started trying out some of the hardening tests on epbs, this one easily breaks the network and it doesn't heal again.
1. Launch network
2. Use snooper API to stop `/eth/v3/validator/blocks`, continue to allow attestations
3. Re-enable block production

When tried with latest releases, the chain heals after some time. With the branch `epbs-devnet-0-sync` it doesnt. 

To reproduce:
clone https://github.com/eth-clients/diamond
Run `SCENARIO=nfin-001 make run-scenario`
Check dora and assertoor outputs
[2026-03-16T17:38:45.436000+00:00] potuz: I forgot to ask you about this during the call, the issue is if you just stop block production and then restart it there's some splitting/takes time to heal? what is the status of attesting in these chains? There's a PR on Prysm that should merge in the next hour or so that should help here
[2026-03-16T17:39:14.094000+00:00] potuz: By status I mean which version of LH and Lodestar are you running, do they send index=1 attestations when there are blocks missing?
[2026-03-16T18:16:54.679000+00:00] parithosh: nft-001
```

</details>
