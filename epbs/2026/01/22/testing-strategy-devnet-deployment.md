# DevNet0 Spec and Local Construction

**Channel:** epbs | **Date:** 2026-01-22

## Summary

The discussion centers around DevNet0 specifications and implementation approach. Potuz confirms that DevNet0 will focus on local construction, with the same client also functioning as a builder. There's acknowledgment that this builder functionality could be more explicitly documented in the specification.

A technical concern is raised by nc1234 regarding the implementation complexity of variable PTC (Payload Timeliness Committee) deadlines for Lodestar. The issue involves how the validator client (VC) should handle payload timeliness determination, specifically needing access to arrival time and execution payload size information. This data would either need to be determined by the VC itself or passed through the beacon API.

The discussion suggests using v1.7.0-alpha-1 for DevNet0, though the variable PTC deadline feature remains a challenging implementation detail that needs resolution for proper validator client integration.

## Participants

- nc1234
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-22T00:56:44.092000+00:00] terencechain: Is there a devnet0 spec? We are only doing local construction right?
[2026-01-22T01:06:33.676000+00:00] potuz: yes
[2026-01-22T01:09:08.640000+00:00] potuz: Well, at the same time another functionality is carried by the same client: being a builder.
[2026-01-22T01:09:40.198000+00:00] potuz: but yeah, I guess it can be explicitly mentioned in the spec
[2026-01-22T01:11:26.801000+00:00] nc1234: I hope we can just use v1.7.0-alpha-1 for devnet 0. I think variable PTC deadline is tricky for Lodestar to implement. Somehow VC needs to know the arrival time and the size of the execution payload. Either VC needs to determine payload timeliness, or this info need to be passed through beacon api
```

</details>
