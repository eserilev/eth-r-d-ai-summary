# Deposit queue bug found by Lido

**Channel:** epbs | **Date:** 2026-02-03

## Summary

Lido discovered a valid deposit queue bug in the Ethereum consensus specifications, as confirmed by potuz who described it as severe enough to warrant another release with a test. The issue is documented in GitHub issue #4896, with a proposed fix in pull request #4897 that aims to minimize impact on staking pools.

The team is working to stabilize the EIP and update it to address this deposit queue issue. There's a call for more client teams to review the proposed fix in PR #4897, indicating that broader consensus and testing is needed before implementation.

**Action items:** Client teams need to review PR #4897, the EIP requires updates, and a new release with tests for this bug appears to be planned given the severity of the issue.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-03T10:09:56.465000+00:00] potuz: <@592004585506340885> the issue found by the Lido guys seems valid to me https://github.com/ethereum/consensus-specs/issues/4896. Haven't looked much cause I need some morning coffee, but woke up to another deposit queue bug
[2026-02-03T10:58:24.498000+00:00] potuz: Regarding this one, this is a possible fix that seems to be the one that will affect pools the least https://github.com/ethereum/consensus-specs/pull/4897
[2026-02-03T15:47:11.264000+00:00] potuz: Need to update the EIP itself. When are we going to get it in a stable place? I think the deposit issue above is severe enough to warrant another release with a test for it
[2026-02-03T16:02:26.364000+00:00] jtraglia: Clients, could we get a few more eyes on this? Please review.
https://github.com/ethereum/consensus-specs/pull/4897
```

</details>
