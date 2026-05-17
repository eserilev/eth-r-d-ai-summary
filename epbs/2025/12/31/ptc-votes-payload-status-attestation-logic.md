# PTC Vote Influence on Attester Behavior

**Channel:** epbs | **Date:** 2025-12-31

## Summary

terencechain raised a technical question about the `get_payload_status_tiebreaker` function, specifically questioning how old Payload Timeliness Committee (PTC) votes could influence attester behavior. They noted that the `should_extend_payload` condition is only reached when dealing with payloads from previous slots, which seems to create a logical inconsistency in their understanding of how PTC votes would affect current attester decisions.

This appears to be an open technical question about the interaction between PTC voting mechanisms and attester behavior in Ethereum's consensus protocol, with no responses or resolution provided in the available discussion.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-31T17:13:43.409000+00:00] terencechain: I must be missing something... in `get_payload_status_tiebreaker` we only reach `should_extend_payload` when the payload is from a previous slot so as an attester how could old PTC votes influence attester votes?
```

</details>
