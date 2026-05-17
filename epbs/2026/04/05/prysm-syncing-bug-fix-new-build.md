# Prysm bug fix and testing

**Channel:** epbs | **Date:** 2026-04-05

## Summary

potuz identified and fixed a bug in Prysm's REST endpoint handler that was causing issues on the Gloas network. The problematic behavior relates to payload content tracking on finalization - while it wouldn't be inherently bad if payload content was properly tracked, it's broken in the current Gloas implementation. potuz noted that the error might not be commonly encountered because the system uses the state's slot and requests payloads from earlier periods to determine sync decisions.

A fix was implemented and merged into the devnet branch (PR #16635), though potuz expressed concern that a simple restart might not resolve the issue immediately and that they may need to sync up a node first to properly serve it. 0xunclebill indicated they would test the changes using their local Prysm node for syncing, though potuz raised the potential problem that the Prysm node itself might have syncing difficulties.

## Participants

- 0xunclebill
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-05T19:26:31.713000+00:00] 0xunclebill: no rush enjoy the holiday!
[2026-04-05T19:29:03.492000+00:00] potuz: yeah I can see the handler for the REST endpoint is fucked up, I'll cook a fix.
[2026-04-05T19:33:12.128000+00:00] potuz: This behavior would not be bad currently, and would in principle not be bad if we actually tracked the payload content on finalization, but on Gloas this is just broken. I think we don't hit this error because we take the slot of the state as given and anyway requests payloads from before to decide if we sync from it or not
[2026-04-05T19:40:44.418000+00:00] potuz: hopefully this fixes this. https://github.com/OffchainLabs/prysm/pull/16635
[2026-04-05T19:47:18.993000+00:00] potuz: Although I'm afraid I'll have to sync up a node with this to be able to serve it to you to start with
[2026-04-05T19:50:39.918000+00:00] potuz: it's on the devnet branch already, but I'm afraid that a restart probably won't work straight away. We'll see
[2026-04-05T20:00:35.270000+00:00] 0xunclebill: no worries I’m running a Prysm node locally that I’m using to sync against. will try it out with the changes in ur branch
[2026-04-05T22:57:39.821000+00:00] potuz: Problem is if that Prysm node can't sync lol
```

</details>
