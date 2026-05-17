# Missing parent block and payload handling mechanisms

**Channel:** epbs | **Date:** 2026-03-03

## Summary

The discussion centers on handling missing parent blocks and payloads during Ethereum block import processes. Potuz inquires about implementations' ability to request both missing parent blocks and missing parent payloads when importing new blocks/payloads. 

0xunclebill explains their current approach: they can cache payloads when parent blocks are missing and import them once the parent arrives, but they don't handle the case of missing parent payloads. Their system remains heavily dependent on gossip networking, though they're developing a "lookup sync" mechanism to address the missing parent payload case and reduce gossip reliance. Potuz notes that the second scenario (missing parent payloads) occurs during short slots but is less frequent.

The conversation touches on performance implications, with Potuz reporting no measurable performance gains from Prysm's corrected pending blocks queue. NC1234 describes their block publishing workflow, which involves sequential API calls without delays except for early envelope publishing, with Potuz suggesting this approach should prevent issues in sane development network environments when using empty payloads.

## Participants

- 0xunclebill
- nc1234
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-03T00:50:18.382000+00:00] potuz: Are you guys able to request a missing parent block as well as a missing parent payload if you're missing them to import a payload/block? I believe you just added the first case right?
[2026-03-03T01:04:04.763000+00:00] 0xunclebill: we are still heavily reliant on gossip, but we'll cache the payload when we haven't seen the parent block yet. and we'll import the cached payload as soon as its parent block has been imported. 

in the other case you mention, we are not handling that at all.

I'm trying to hack together what we call lookup sync, which should handle the second case you mention and make us less reliant on gossip in general.
[2026-03-03T01:05:14.064000+00:00] potuz: Yeah the second part also happens with short slots but it's less likely
[2026-03-03T01:06:00.849000+00:00] potuz: Fwiw I didn't see any performance gain on Prysm with or without the corrected pending blocks queue
[2026-03-03T02:50:28.600000+00:00] nc1234: We don’t really have a delay. We call publish block endpoint, wait for api to return a response, then call publish envelope endpoint. We will only wait in publish envelope if the endpoint is called early ie. if it is called in slot n - 1, it will wait and publish on the 0th second of slot n
[2026-03-03T02:54:32.732000+00:00] potuz: That should be enough to not trigger this issue on a sane host devnet. With empty payloads
```

</details>
