# Assertoor Builder Deposit Spam Test Setup and Debugging

---

## 2026-05-10 (epbs)

The discussion centers around debugging issues with the Assertoor builder deposit spam test setup. Initially, terencechain encountered an error where the test failed to initialize due to an "unknown task name: generate_batch_deposits". jtraglia resolved this by explaining that support for this functionality was only merged into the master branch 3 days prior, requiring a fresh pull of the `ethpandaops/assertoor:master` Docker image.

After updating the image, terencechain faced a configuration validation error requiring either limitPerSlot, limitTotal, or indexCount to be set, which was resolved by setting the value to 1000. When attempting to scale up to 8192 deposits, the test hit a transaction size limit (1,442,245 bytes vs 131,072 limit), prompting jtraglia to suggest increasing the gas limit from 150m to 200m to accommodate the larger batch size.

The discussion successfully progressed from initial setup issues through configuration problems to scaling considerations, with the final state being a need to adjust gas limits for larger deposit batches.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-10T15:08:39.740000+00:00] terencechain: ```
2026-05-10 08:05:21 PDT
time="2026-05-10T15:05:21Z" level=error msg="could not schedule startup test execution for builder-deposit-spam (Spam chain with batched 0x03 builder deposits): failed initializing test run #1 'Spam chain with batched 0x03 builder deposits': unknown task name: generate_batch_deposits"
```
[2026-05-10T15:15:20.758000+00:00] jtraglia: The `ethpandaops/assertoor:master` image should have support for that. Maybe you need to force pull new images?
[2026-05-10T15:17:42.377000+00:00] jtraglia: Support for this was only merged into master 3 days ago btw.
[2026-05-10T16:36:24.829000+00:00] terencechain: nice! that tells you how much i know about docker 🙂 
new failure but i can figure it out...
```
[ERR] config validation failed: either limitPerSlot or limitTotal or indexCount must be set
```
[2026-05-10T16:51:54.332000+00:00] terencechain: 1000 works
[2026-05-10T16:52:02.773000+00:00] terencechain: i tried to go 8192 deposits and got this from assertor: `failed sending batch tx: oversized data: transaction size 1442245, limit 131072`
[2026-05-10T16:52:51.913000+00:00] jtraglia: Nice. Are you using 150m gas limit? Needs 200m for 8192.
```

</details>

---

## 2026-05-14 (epbs)

Potuz inquired about whether there are existing tests that cover scenarios with repeated deposits for the same builder in the queue before a fork occurs. Jtraglia confirmed that such tests do exist and provided a direct link to the specific test implementation in the Ethereum consensus specs repository at `test_gloas_fork_onboard_builders.py`.

The discussion appears to be part of testing efforts for the Gloas fork, specifically around builder deposit functionality and queue management scenarios. No open questions or additional action items were identified, as the testing concern was promptly resolved with the provided test reference.

**Participants:** jtraglia, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-14T12:01:38.589000+00:00] potuz: <@592004585506340885> do you know if we have tests with repeated deposits for the same builder in the queue pre-fork?
[2026-05-14T13:36:09.676000+00:00] jtraglia: Yes, the test for that is [here](https://github.com/ethereum/consensus-specs/blob/fa8bb08022199cfd59f8402c9ec12d9b7f8f6e5e/tests/core/pyspec/eth_consensus_specs/test/gloas/fork/test_gloas_fork_onboard_builders.py#L301-L332).
```

</details>

