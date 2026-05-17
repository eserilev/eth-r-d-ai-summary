# Payload Blinding for Disk Space Optimization

**Channel:** epbs | **Date:** 2026-02-27

## Summary

The discussion centers on a disk space optimization strategy where Ethereum clients would "blind" their payloads and use the `engine_getPayloadBodiesBy*` API methods to reconstruct them when needed. The initial question from ethdreamer seeks to understand if other client implementations are planning to adopt this approach as a way to reduce storage requirements.

The conversation appears to be in its early stages, with jtraglia simply acknowledging the concept of "blinding payloads to save disk space." No concrete implementation plans, consensus, or technical details about the blinding mechanism were discussed. The question of cross-client adoption of this optimization strategy remains open.

## Participants

- ethdreamer
- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-27T17:06:23.406000+00:00] ethdreamer: Are other clients planning to blind their payloads to save disk space and rely on `engine_getPayloadBodiesBy*` to reconstruct them?
[2026-02-27T17:36:57.544000+00:00] jtraglia: Blinding payloads to save disk space
```

</details>
