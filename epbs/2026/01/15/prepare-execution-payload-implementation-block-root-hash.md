# prepare_execution_payload implementation and block root vs hash

**Channel:** epbs | **Date:** 2026-01-15

## Summary

jtraglia asked for feedback on a change to the `prepare_execution_payload` implementation and whether they should use `parent_beacon_block_root=state.latest_block_hash`, questioning if these two values are equivalent.

potuz clarified that these are different things: one is a block root from the consensus layer (CL) while the other is a block hash from the execution layer (EL). jtraglia acknowledged the distinction and thanked potuz for the clarification.

The discussion resolved the confusion about the difference between consensus layer block roots and execution layer block hashes, though no final decision was made about the specific implementation change.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-15T21:28:55.990000+00:00] jtraglia: <@755590043632140352> does this change to `prepare_execution_payload` look correct?
[2026-01-15T21:29:59.957000+00:00] jtraglia: 
[2026-01-15T21:31:37.166000+00:00] jtraglia: I'm wondering if we should do:
`parent_beacon_block_root=state.latest_block_hash`
Are these the same thing?
[2026-01-15T21:36:54.285000+00:00] potuz: AFK now but those are different things one is a block root (CL) the other is a block hash (EL)
[2026-01-15T21:39:05.170000+00:00] jtraglia: Ah oops. Yeah that makes sense. Thanks.
```

</details>
