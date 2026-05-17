# PTC Attestations and Payload Attestations in Devnet

**Channel:** epbs | **Date:** 2026-03-08

## Summary

The discussion centers on implementation decisions for PTC (Payload Timeliness Committee) attestations in the current devnet environment. Potuz indicates that there needs to be some form of agreement among participants before proceeding with sending PTC attestations, and suggests that one unexplored option is to not implement weight selection at all.

Terencechain raises a security concern, noting that some clients are already sending payload attestations in the devnet, which creates a potential exploitation vulnerability in the current setup. This suggests there may be an urgent need to address the attestation mechanism before the security risk becomes problematic.

## Participants

- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-08T00:45:17.209000+00:00] potuz: Regarding this, we need some form of agreement before deciding to send PTC attestations in the devnet. The option that hasn't been mentioned is simply not so weight selection at all
[2026-03-08T00:47:49.805000+00:00] terencechain: i was under the impression some clients are already sending payload attestations in the devnet, i think it already can be exploited today
```

</details>
