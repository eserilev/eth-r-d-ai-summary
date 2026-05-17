# Missing Payload Testing and Client Interop

**Channel:** epbs | **Date:** 2026-02-23

## Summary

**Potuz** raised concerns about testing missing payload scenarios, which previously caused interoperability issues between Prysm and Teku clients. He emphasized that this type of testing should be the primary focus of devnet0, as it's a critical failure mode that needs validation.

**0xunclebill** committed to testing intentionally missing payloads later that day. **Potuz** then outlined a detailed testing scenario involving a 70% Lighthouse / 30% Lodestar split where payloads are withheld from broadcast but retained in Lighthouse nodes. The test would verify that Lodestar can request missing payloads via RPC from Lighthouse and that both clients handle the resulting reorg scenarios appropriately.

The main concern is ensuring clients can continue syncing blocks and maintain network cohesion rather than splitting when encountering missing payloads. While **Potuz** expressed confidence about the reorg handling aspects, the core focus remains on basic client interoperability during payload unavailability scenarios.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-23T15:03:19.121000+00:00] potuz: <@586161934425128960> <@358120958726373381> have you guys tried with a missing payload? that's what broke Prysm-Teku interop very early on
[2026-02-23T15:03:30.250000+00:00] potuz: and that's essentially the only thing that devnet0 should be testing
[2026-02-23T15:15:55.402000+00:00] 0xunclebill: I’ll try intentionally not publishing payloads a bit later today and see what happens
[2026-02-23T15:38:09.090000+00:00] potuz: It's a little involved, say you run with 70% LH and 30% Lodestar, you want to avoid publishing the payload but still have it in the LH nodes, so the LH nodes will attest to their blocks and build on full, we want to check that LH 
1) Lodestar requests payloads by RPC
2) LH serves those payloads. 
3) Lodestars tries to reorg some of these payloads cause it hasn't gotten them on time to process
4) LH reorgs back the block that reorged the payload.
[2026-02-23T15:38:49.134000+00:00] potuz: I'm not worried about the latest two, but I do want to check that clients can continue syncing blocks and don't entirely split up if there is a single missing payload
```

</details>
