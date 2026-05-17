# Kurtosis deposit stress testing configuration

**Channel:** epbs | **Date:** 2026-05-09

## Summary

Terencechain requested a Kurtosis configuration to stress test Ethereum deposits at 8192 or higher levels to verify Prysm optimizations can process them efficiently under minimal slot time. Jtraglia provided links to relevant resources and mentioned these were related to deposit signature verification optimizations.

Terencechain attempted to implement a configuration using the Gloas fork with assertoor testing parameters set to spam 1000 deposits per block (200 batch size × 5 pending batches), but encountered issues where no deposit requests were being generated. When trying to start the test, they received a "500 Internal Server Error" API error, leaving the stress testing configuration unresolved.

The discussion remains open with technical issues preventing the deposit stress testing from proceeding as intended.

## Participants

- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-09T16:06:23.887000+00:00] terencechain: hi, is there a kurtosis config to stress test 8192 or even higher deposits? i have optimizations i want to test and verify prysm can process them timely even under minimal slot time
[2026-05-09T16:08:01.941000+00:00] jtraglia: Hey there. Maybe try this: https://discord.com/channels/595666850260713488/814925424726900766/1502307212138254568
[2026-05-09T16:08:56.376000+00:00] jtraglia: Ah actually, this is a better link: https://discord.com/channels/595666850260713488/874767108809031740/1502074536932409476
[2026-05-09T16:20:00.251000+00:00] jtraglia: Deposit signature verification optimizations
[2026-05-09T18:03:38.592000+00:00] terencechain: i tried but there's no deposit request coming in... 
```
network_params:
  gloas_fork_epoch: 1
  preset: minimal
  withdrawal_type: "0x01"
  validator_balance: 40000

additional_services:
  - dora
  - assertoor
  - spamoor
  - checkpointz

assertoor_params:
  image: ethpandaops/assertoor:master
  run_stability_check: false
  run_block_proposal_check: false
  tests:
    - file: "https://raw.githubusercontent.com/ethpandaops/assertoor/refs/heads/master/playbooks/gloas-dev/builder-deposit-spam.yaml"
      config:
        batchSize: 200
        pendingBatches: 5 # 5*200 = 1000 deposits per block
        totalDeposits: 0  # 0 = unlimited

dora_params:
  image: ethpandaops/dora:master
```
[2026-05-09T18:13:10.829000+00:00] terencechain: says pending, i cant start it.. got `API error: 500 Internal Server Error`
```

</details>
