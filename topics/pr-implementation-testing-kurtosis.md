# PR Implementation Testing with Kurtosis

---

## 2026-04-12 (epbs)

Terencechain reported successful implementation and testing of consensus spec PR #5094 in Prysm using Kurtosis with multiple beacon nodes, achieving finalization. While the implementation appears to conform to the specification, they identified some new performance bottlenecks on the Prysm side during testing.

They are now seeking a Kurtosis configuration example that can generate Execution Layer (EL) requests, suggesting they want to expand their testing scope beyond the current setup. This remains an open question without a response in the current discussion.

**Participants:** terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-12T14:24:13.033000+00:00] terencechain: i implemented https://github.com/ethereum/consensus-specs/pull/5094 for prysm and got it to finalized with kurtosis with multiple beacon nodes. i noted some new prysm side bottlenecks with this change, but as of spec, it looks good with me. 

do we have a kurtosis config example somewhere that can be used to generate EL requests?
```

</details>

---

## 2026-04-13 (epbs)

The discussion centers around setting up execution request testing in Kurtosis, with terencechain asking for an easy way to trigger execution requests. barnabasbusa provides two approaches: automated testing using assertoor with YAML configuration that includes BLS changes and builder lifecycle tests, or manual testing by creating withdrawal credentials (0x02) and using Dora's web UI for partial withdrawals.

pk910 suggests using a specific test file (`fillup-all-el-queues.yaml`) that fills execution requests with deposits, consolidation requests, and withdrawal requests, though notes these requests are invalid but should still work for testing purposes. barnabasbusa mentions that consolidations may require specific network parameters due to minimum ETH requirements, highlighting a potential configuration consideration for comprehensive testing setups.

**Participants:** barnabasbusa, pk910, terencechain

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-21 (epbs)

Stefan encountered a Kurtosis error while working with the ethereum-package, specifically an "undefined: GpuConfig" error in the zkboost_launcher.star file at line 272. The error suggested "get_config?" as a potential alternative, indicating a naming or import issue.

Barnabas quickly identified this as likely a version issue, asking if Stefan was running Kurtosis 1.18.1 and directing him to a previous Discord message that apparently contained the solution. Stefan confirmed he had missed the earlier message and that the issue was resolved after reviewing it.

The discussion was brief and resolved quickly, with the root cause appearing to be related to a specific Kurtosis version or a previously documented issue with the GpuConfig definition in the ethereum-package zkboost launcher.

**Participants:** barnabasbusa, stefanbratanov

<details>
<summary>Raw messages</summary>

```
[2026-04-21T15:02:01.777000+00:00] stefanbratanov: I am getting this weird error on Kurtosis, any idea <@412614104222531604> ?

```
INFO[2026-04-21T16:01:00+01:00] Enclave 'test-epbs' created successfully
There was an error interpreting Starlark code
Evaluation error: Multiple errors caught interpreting the Starlark script. Listing each of them below.
        at [github.com/ethpandaops/ethereum-package/src/zkboost/zkboost_launcher.star:272:17]: undefined: GpuConfig (did you mean get_config?)
        at [github.com/ethpandaops/ethereum-package/main.star:67:24]: <toplevel>

Error encountered running Starlark code.
```
[2026-04-21T15:02:24.096000+00:00] barnabasbusa: are you running 1.18.1?
[2026-04-21T15:02:44.760000+00:00] barnabasbusa: https://discord.com/channels/595666850260713488/892088344438255616/1493984222422237247 pls see this message
[2026-04-21T15:04:22.242000+00:00] stefanbratanov: got it, missed that message, all good now, thanks
```

</details>

