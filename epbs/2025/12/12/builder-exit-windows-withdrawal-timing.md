# Builder exit windows and withdrawal timing

**Channel:** epbs | **Date:** 2025-12-12

## Summary

The discussion focuses on the design of exit windows for builders in Ethereum's consensus layer. Potuz explains that implementing a large exit window (slow withdrawals) for builders would mitigate potential attacks where malicious actors deposit and immediately withdraw just to bloat the beacon state. The participants agree on a model with immediate deposits but very slow withdrawals for builders, with jtraglia suggesting a 4096 epoch window (approximately 18 days).

Potuz proposes that builders could be onboarded before a fork by modifying the deposit processing logic to route deposits to either the builder or validator depending on availability in the "builder slice." The group clarifies that this withdrawal delay mechanism would only affect exits, not builder payments, which should remain near-instant to maintain normal operational flow.

An open question remains about how deposits and withdrawals would be handled for validators after a builder with the same public key exits the system, indicating this aspect of the design still needs resolution.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T17:01:10.561000+00:00] jtraglia: Can you expand upon this? I don't understand.
[2025-12-12T17:03:57.104000+00:00] potuz: I mean that if we make a large window for exits, then the attack of depositing and withdrawing just to blow the beacon state is not really an issue.
[2025-12-12T17:04:24.843000+00:00] jtraglia: Large window == slow exits/withdrawals for builders?
[2025-12-12T17:04:31.332000+00:00] potuz: yes, for builders
[2025-12-12T17:04:43.152000+00:00] jtraglia: Yes. We can do this 👍
[2025-12-12T17:04:43.981000+00:00] potuz: should be immediate deposit, very slow withdrawal
[2025-12-12T17:04:53.699000+00:00] jtraglia: Agreed 👍
[2025-12-12T17:05:35.626000+00:00] jtraglia: 4096 epochs (18 days)?
[2025-12-12T17:05:42.108000+00:00] jtraglia: More?
[2025-12-12T17:06:26.040000+00:00] potuz: I honestly don't see why we can't onboard them pre-fork. When processing deposits you can just deposit to the builder one if there is one in the builder slice and if not, deposit to the validator.
[2025-12-12T17:07:20.423000+00:00] jtraglia: This shouldn't affect builder payments though, right? Payments == ~instant; exits == very slow?
[2025-12-12T17:07:49.944000+00:00] potuz: yeah this doesn't change builder payments
[2025-12-12T17:13:47.726000+00:00] jtraglia: How would you make deposits/withdrawals to the validator after the the builder exists with the same pubkey?
```

</details>
