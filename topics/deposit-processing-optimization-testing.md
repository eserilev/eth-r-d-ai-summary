# Deposit processing optimization and testing strategy

---

## 2026-05-07 (epbs)

The discussion centers around optimizing Ethereum deposit processing performance. Initially, processing 8192 deposits took 7 seconds, but terencechain implemented an optimization that reduced this to 50ms in the latest commit. The optimization strategy involves a divide-and-conquer approach with a cutoff to linear processing, though benchmarking was still ongoing.

jtraglia requested creating assertoor tests with 8192 deposits containing varying numbers of invalid signatures (1, 10, 100, and 1000 invalid signatures) to test edge cases. The rationale was initially based on concerns about aggregate signature optimization, but jtraglia later realized this wouldn't apply to deposits since they involve different messages. pk910 noted they had already created a test for valid builder deposits using batches of 200 deposits with 5 pending batches per block.

The main action item is creating the requested test suite with invalid signatures, though the original technical justification was retracted by the end of the discussion.

**Participants:** jtraglia, pk910, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-07T22:01:56.056000+00:00] jtraglia: <@363800010518822915> said it took 7s to process 8192 deposits.
https://discord.com/channels/595666850260713488/1501968885434421339/1501985572410097726
[2026-05-07T22:02:34.096000+00:00] terencechain: that's before my optimization though, i already solved it in the latest commit.. 50ms now
[2026-05-07T22:02:40.525000+00:00] jtraglia: Hell yeah. Okay cool.
[2026-05-07T22:03:00.177000+00:00] jtraglia: What is the optimization? What if there's an invalid signature mixed in there?
[2026-05-07T22:13:36.259000+00:00] terencechain: still benchmarking this.. but i think the best strategy is something like divide and conquer but with a cutoff to linear
[2026-05-07T22:24:44.362000+00:00] jtraglia: <@808969530608451584> <@412614104222531604> for tomorrow, could we have an assertoor test which has 8192 deposits where 1 sig is invalid, 10 sigs are invalid, 100 sigs are invalid, 1000 sigs are invalid. It should be somewhat randomized too.
[2026-05-07T22:27:56.800000+00:00] pk910: what's the reason behind a test with invalid signatures?
we've just created a test to do valid builder deposits (all unique):
```yaml
# kurtosis config snippet
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
```
[2026-05-07T22:39:22.374000+00:00] jtraglia: I was thinking some clients my try to optimize this with an aggregate signature check. But invalid signatures would force them to check them individually, which is slower.
[2026-05-07T22:41:45.983000+00:00] jtraglia: Given they are different messages (deposits), I guess that's not possible. Ignore me. I'm tired.
```

</details>

---

## 2026-05-09 (epbs)

Terencechain requested a Kurtosis configuration to stress test 8192 or higher deposits to verify Prysm optimizations for deposit signature verification under minimal slot time conditions. Jtraglia provided links to relevant resources and identified the optimizations as being related to deposit signature verification.

Terencechain attempted to use a configuration targeting 1000 deposits per block (200 batch size × 5 pending batches) using the Gloas fork with minimal preset, but encountered issues where no deposit requests were coming in initially. When trying to start the test, they received a "500 Internal Server Error" from the API, indicating the stress test setup was not functioning properly.

The discussion ends with an unresolved technical issue preventing the deposit stress testing from proceeding, leaving the verification of Prysm's deposit processing optimizations incomplete.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

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

