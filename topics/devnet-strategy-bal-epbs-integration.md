# Devnet Strategy: BAL and ePBS Integration

---

## 2026-04-14 (epbs)

The discussion centers on the integration strategy for BAL (Balance) and ePBS (enshrined Proposer-Builder Separation) devnets. barnabasbusa initially questioned whether combining two unstable devnets would produce stability, preferring that each component be stabilized independently before merging.

potuz argued that since BAL is unlikely to introduce consensus bugs and can be tested independently on the execution layer, it makes sense to build the ePBS devnet on top of BAL. He noted that while EL developers may want to maintain a separate BAL devnet longer, a pure ePBS devnet would likely be useless. barnabasbusa maintained preference for achieving a stable BAL devnet before integrating ePBS changes.

qu0b provided a brief status update indicating they are close to stability, currently working on getting geth to sync and the network to finalize. The discussion leaves open the question of whether to proceed with integration before achieving full BAL stability or wait for independent stabilization first.

**Participants:** barnabasbusa, potuz, qu0b

<details>
<summary>Raw messages</summary>

```
[2026-04-14T04:17:04.697000+00:00] barnabasbusa: having two unstable devnets will result one stable one ?
[2026-04-14T04:17:28.249000+00:00] barnabasbusa: ideally bals and epbs gets stable on their own and then we merge the two
[2026-04-14T10:42:25.637000+00:00] potuz: Yeah I think it's very unlikely that BAL component makes consensus bugs. If it's already tested on the EL on it's own then it's good for the ePBS devnet to be based on top of bal
[2026-04-14T10:42:59.278000+00:00] potuz: The converse is not true and EL devs may want to keep a Bal devnet going longer. But a pure ePBS one is probably useless
[2026-04-14T11:34:25.216000+00:00] barnabasbusa: I'd feel a lot better if we at least had a stable bal devnet before we merge in the changes with epbs.
[2026-04-14T11:34:45.013000+00:00] qu0b: We're not that far off i hope
[2026-04-14T11:34:55.947000+00:00] qu0b: trying to get geth to sync up, working on getting the network to finalize now
```

</details>

