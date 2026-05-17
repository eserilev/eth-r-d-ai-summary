# Builder Deposit Queue Testing

**Channel:** epbs | **Date:** 2026-05-14

## Summary

In this brief discussion, potuz asked jtraglia about the existence of tests that cover scenarios where the same builder has repeated deposits in the queue before a fork occurs. This appears to be related to testing the builder deposit queue functionality in the context of an upcoming fork.

Jtraglia confirmed that such tests do exist and provided a direct link to the specific test implementation in the Ethereum consensus specifications repository. The test is located in the `test_gloas_fork_onboard_builders.py` file and covers the repeated deposits scenario that potuz was inquiring about.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-14T12:01:38.589000+00:00] potuz: <@592004585506340885> do you know if we have tests with repeated deposits for the same builder in the queue pre-fork?
[2026-05-14T13:36:09.676000+00:00] jtraglia: Yes, the test for that is [here](https://github.com/ethereum/consensus-specs/blob/fa8bb08022199cfd59f8402c9ec12d9b7f8f6e5e/tests/core/pyspec/eth_consensus_specs/test/gloas/fork/test_gloas_fork_onboard_builders.py#L301-L332).
```

</details>
