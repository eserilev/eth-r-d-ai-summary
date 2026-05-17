# Execution requests testing through Kurtosis

**Channel:** epbs | **Date:** 2026-04-13

## Summary

The discussion centers around testing execution requests in Ethereum using Kurtosis, initiated by terencechain asking for an easy triggering method. barnabasbusa provided both automated and manual approaches: for automation, they shared a YAML configuration using assertoor with tests for BLS changes and builder lifecycle, while for manual testing they suggested creating withdrawal credentials with 0x02 and using Dora's web UI for partial withdrawals.

pk910 contributed an additional test configuration (`fillup-all-el-queues.yaml`) that fills execution request queues with deposits, consolidation requests, and withdrawal requests, though they noted these requests are invalid but should still work for testing purposes. barnabasbusa pointed out that consolidation testing may require specific network parameters due to minimum ETH requirements, highlighting a potential configuration consideration for comprehensive testing.

## Participants

- barnabasbusa
- pk910
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-13T13:31:25.757000+00:00] terencechain: Is there an easy way to trigger execution request through kurtosis?
[2026-04-13T13:31:53.823000+00:00] barnabasbusa: you want automated way, or manual?
[2026-04-13T13:32:31.657000+00:00] barnabasbusa: ```yaml
additional_services:
  - assertoor

assertoor_params:
  run_stability_check: false
  run_block_proposal_check: false
  tests:
    - { file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/master/playbooks/pectra-dev/kurtosis/bls-changes.yaml", config: { targetAddress: "0x332E43696A505EF45b9319973785F837ce5267b9" } }
    - { file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/gloas-support/playbooks/gloas-dev/builder-lifecycle.yaml" }
```
[2026-04-13T13:32:40.609000+00:00] barnabasbusa: this should have some bls changes and builder lifecycle
[2026-04-13T13:33:04.551000+00:00] barnabasbusa: if you want manual you can create withdrawal credential 0x02 and use dora's webui to trigger partial withdrawals
[2026-04-13T13:34:47.625000+00:00] pk910: running this test should fill the Execution Requests with deposits, consolidation- & withdrawalrequests:
```
    - { file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/master/playbooks/pectra-dev/kurtosis/fillup-all-el-queues.yaml" }
```
[2026-04-13T13:35:04.756000+00:00] barnabasbusa: I think consolidations will require a specific network param tho
[2026-04-13T13:35:13.536000+00:00] barnabasbusa: due to min eth
[2026-04-13T13:35:33.110000+00:00] pk910: it's only filling the queue.  the requests are invalid.  but shouldn't matter much for testing?
```

</details>
