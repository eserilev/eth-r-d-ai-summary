# Devnet0 Timeline and Testing Requirements

**Channel:** epbs | **Date:** 2026-03-02

## Summary

The team is discussing the timeline for launching Devnet0, with a deadline set for Wednesday of that week. The goal is to achieve 4-client interoperability testing on Kurtosis before the deadline, though there are concerns about sync functionality being ready in time.

Potuz suggests they could potentially meet the deadline even without sync working initially, and mentions plans to test against Lodestar since it can handle early payloads. Barnabasbusa agrees that sync testing can be done in parallel, with syncing functionality targeted for Friday or the following Monday. The consensus is to proceed with a "happy case" devnet initially, focusing on basic functionality rather than stress testing.

The main concern raised is network stability - if issues arise in the simplified devnet, it could fail quickly and require regeneration. However, since no aggressive testing is planned initially, the expectation is that the network should remain stable during basic operations.

## Participants

- barnabasbusa
- potuz
- qu0b

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-02T14:46:09.809000+00:00] potuz: So #wendevnet0?
[2026-03-02T14:46:15.535000+00:00] potuz: <@412614104222531604>
[2026-03-02T14:46:25.175000+00:00] qu0b: whats the deadline
[2026-03-02T14:46:34.995000+00:00] barnabasbusa: Deadline is wed this week
[2026-03-02T14:46:46.645000+00:00] barnabasbusa: ideally we have 4 client interop on kurtosis before that
[2026-03-02T14:48:13.179000+00:00] potuz: Sounds good to me if we don't care about sync
[2026-03-02T14:48:23.501000+00:00] potuz: even with sync we may make it
[2026-03-02T14:48:47.066000+00:00] potuz: Will test agains Lodestar in a bit since Nico said they can handle early payloads
[2026-03-02T14:50:38.516000+00:00] barnabasbusa: We can test syncing in parallel. its probably fine to have syncing working by friday/next mon
[2026-03-02T14:50:52.982000+00:00] barnabasbusa: I would be happy with a happy case devnet for now
[2026-03-02T14:51:36.822000+00:00] potuz: issue is if by any reason it's not happy, then it's quickly dead, if it's no problem for you to keep regenerating it, we'll be happy
[2026-03-02T14:53:29.411000+00:00] barnabasbusa: we won't be testing anything crazy
[2026-03-02T14:53:33.498000+00:00] barnabasbusa: so it should just survive 😄
```

</details>
