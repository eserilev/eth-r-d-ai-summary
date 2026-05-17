# Builder Lifecycle Testing Progress

**Channel:** epbs | **Date:** 2026-03-18

## Summary

pk910 shared an assertoor test configuration for Kurtosis that enables builder lifecycle testing, including builder deposits and voluntary/execution layer triggered exits. The test can be added to Kurtosis configs using a specific YAML configuration that points to a builder-lifecycle.yaml playbook from the gloas-support branch.

barnabasbusa provided progress updates on the testing, initially indicating issues but later reporting success with "LFG" (let's go). The builder exits are now functioning properly on Prysm/Lodestar networks, though Lighthouse is still experiencing difficulties with the implementation. This suggests the builder lifecycle functionality is working across most client implementations but requires further debugging for Lighthouse compatibility.

## Participants

- barnabasbusa
- pk910

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-18T13:16:34.886000+00:00] pk910: you can add this assertoor test to your kurtosis configs to get some builder deposits & voluntary+el triggered exits once they're active:
```yaml
additional_services:
  - dora
  - assertoor

assertoor_params:
  run_stability_check: false
  run_block_proposal_check: false
  tests:
    # bls changes + withdrawals
    #- { file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/master/playbooks/pectra-dev/kurtosis/bls-changes.yaml", config: { targetAddress: "0x332E43696A505EF45b9319973785F837ce5267b9" } }
    # builder deposits + exits
    - { file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/gloas-support/playbooks/gloas-dev/builder-lifecycle.yaml" }
```
[2026-03-18T13:18:55.105000+00:00] barnabasbusa: 
[2026-03-18T13:18:59.721000+00:00] barnabasbusa: not looking too hot yet
[2026-03-18T14:46:15.393000+00:00] barnabasbusa: 
[2026-03-18T14:46:19.033000+00:00] barnabasbusa: LFG
[2026-03-18T14:46:34.855000+00:00] barnabasbusa: builder exits now working with prysm/lodestar network, lighthouse is still struggling
```

</details>
