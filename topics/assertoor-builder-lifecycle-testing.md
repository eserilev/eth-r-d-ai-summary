# Assertoor testing and builder lifecycle

---

## 2026-03-18 (epbs)

pk910 shared an Assertoor test configuration for testing builder deposits and voluntary/execution layer triggered exits in Kurtosis environments. The configuration includes a specific test file from the `gloas-support` branch that handles builder lifecycle testing, with other tests like BLS changes commented out.

barnabasbusa reported testing progress, initially indicating issues but later confirming success with "LFG" (let's go). The testing revealed compatibility differences across consensus clients - builder exits are now working correctly with Prysm/Lodestar networks, while Lighthouse is still experiencing difficulties with the builder exit functionality.

The discussion highlights ongoing development work on builder lifecycle management across different Ethereum consensus client implementations, with Lighthouse requiring additional work to achieve full compatibility.

**Participants:** barnabasbusa, pk910

<details>
<summary>Raw messages</summary>

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

