# EIP-7928 Integration and Merge Conflicts with ePBS

**Channel:** epbs | **Date:** 2026-04-14

## Summary

The discussion centers on integrating EIP-7928 with ePBS (enshrined Proposer-Builder Separation) and managing potential merge conflicts. Terencechain notes that while having two substantial parallel additions could create merge conflicts, the overlapping areas appear fairly isolated, affecting the beacon state, payload, and Engine API calls. They offer to work on merging EIP-7928 into the consensus specs once it's more finalized.

The team discusses the approach for managing two unstable devnets, with barnabasbusa preferring to stabilize both BAL (EIP-7928) and ePBS individually before merging them. However, potuz suggests that since BAL is unlikely to introduce consensus bugs and can be tested on the execution layer independently, running an ePBS devnet on top of BAL would be beneficial, noting that a pure ePBS devnet would be less useful while EL developers may want to maintain a separate BAL devnet longer.

The discussion concludes with barnabasbusa expressing preference for achieving a stable BAL devnet before merging with ePBS changes, while qu0b indicates they're close to this goal and currently working on getting geth to sync and the network to finalize.

## Participants

- barnabasbusa
- potuz
- qu0b
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-14T01:57:12.084000+00:00] terencechain: The only downside is that having two substantial additions in parallel can make merge conflicts a pain to deal with on the client side. That said, it prob will not be too bad. From a quick look, the overlapping areas seem fairly isolated. EIP-7928 appears on beacon state, the payload, and the Engine API call

Is there a consensus-specs PR that reflects the latest progress on merging EIP-7928 into the gloas spec folder? I can take this on in a day or two once the spec is a bit more finalized <@1126229231572094976>
[2026-04-14T04:17:04.697000+00:00] barnabasbusa: having two unstable devnets will result one stable one ?
[2026-04-14T04:17:28.249000+00:00] barnabasbusa: ideally bals and epbs gets stable on their own and then we merge the two
[2026-04-14T10:42:25.637000+00:00] potuz: Yeah I think it's very unlikely that BAL component makes consensus bugs. If it's already tested on the EL on it's own then it's good for the ePBS devnet to be based on top of bal
[2026-04-14T10:42:59.278000+00:00] potuz: The converse is not true and EL devs may want to keep a Bal devnet going longer. But a pure ePBS one is probably useless
[2026-04-14T11:34:25.216000+00:00] barnabasbusa: I'd feel a lot better if we at least had a stable bal devnet before we merge in the changes with epbs.
[2026-04-14T11:34:45.013000+00:00] qu0b: We're not that far off i hope
[2026-04-14T11:34:55.947000+00:00] qu0b: trying to get geth to sync up, working on getting the network to finalize now
```

</details>
