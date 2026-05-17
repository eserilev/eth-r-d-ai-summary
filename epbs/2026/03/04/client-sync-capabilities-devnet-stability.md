# Devnet configuration setup and client distribution

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around configuring a devnet with distributed Ethereum client combinations. Barnabasbusa proposed a YAML configuration deploying nodes on Hetzner cloud, with 1 bootnode and varying numbers of consensus/execution client pairs: 3 nodes each for Lighthouse-Geth (validators 0-600), Prysm-Geth (600-1200), and Lodestar-Geth (1200-1800), plus 1 node each for Nimbus-Geth and Teku-Geth with smaller validator ranges. After discussion, they agreed to increase Teku's allocation to 3 nodes for better testing coverage.

Technical specifications were discussed, with 8 cores/16GB RAM/160GB storage proposed for devnet 0 nodes. Barnabasbusa requested access to stefanbratanov's pull request for local testing before deploying to the devnet. There was also discussion about validator key types at genesis, with barnabasbusa preferring 0x02 validators over 0x00 for easier testing of operations without BLS key complications.

The main action items include stefanbratanov providing the PR link and updating the Teku configuration to use 3 nodes instead of 1, while potuz expressed indifference about the validator key type as long as 0x02 and 0x03 keys aren't confused.

## Participants

- barnabasbusa
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T12:28:17.030000+00:00] barnabasbusa: ```yaml
default = [
    { name = "bootnode", count = 1, cloud = "hetzner" },
    { name = "lighthouse-geth", count = 3, cloud = "hetzner", validator_start = 0, validator_end = 600 },
    { name = "prysm-geth", count = 3, cloud = "hetzner", validator_start = 600, validator_end = 1200 },
    { name = "lodestar-geth", count = 3, cloud = "hetzner", validator_start = 1200, validator_end = 1800 },
    { name = "nimbus-geth", count = 1, cloud = "hetzner", validator_start = 1800, validator_end = 1810 },
    { name = "teku-geth", count = 1, cloud = "hetzner", validator_start = 1810, validator_end = 1820 },
  ]
```
[2026-03-04T12:28:21.257000+00:00] barnabasbusa: happy with the config?
[2026-03-04T12:40:46.342000+00:00] barnabasbusa: would you want me to give teku a bit more validator shares?
[2026-03-04T12:42:10.381000+00:00] stefanbratanov: we could try 3 as well
[2026-03-04T12:42:58.607000+00:00] barnabasbusa: ok
[2026-03-04T12:43:04.907000+00:00] barnabasbusa: can you point me to your pr?
[2026-03-04T12:43:17.664000+00:00] barnabasbusa: I'd like to test locally against it
[2026-03-04T12:43:26.987000+00:00] barnabasbusa: and can roll it out on the devnet as well, and then use master once it gets merged in
[2026-03-04T12:49:50.339000+00:00] barnabasbusa: 8c/16gb/160gb should be sufficient for devnet 0 right?
[2026-03-04T12:52:01.272000+00:00] barnabasbusa: do we prefer to have 0x02 or 0x00 validators at genesis?
[2026-03-04T12:52:13.284000+00:00] barnabasbusa: (I'd go with 0x02, as we can potentially test a lot more operations with that)
[2026-03-04T12:52:21.465000+00:00] barnabasbusa: without needing to murk around with bls keys
[2026-03-04T12:53:06.210000+00:00] potuz: honestly got no clue, I assume yes
[2026-03-04T12:53:31.936000+00:00] potuz: I don't care about this one as long as you don't mess up 0x02 with 0x03
```

</details>
