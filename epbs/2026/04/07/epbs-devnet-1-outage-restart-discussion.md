# EPBS Devnet-1 Outage and Restart Discussion

**Channel:** epbs | **Date:** 2026-04-07

## Summary

The EPBS devnet-1 has experienced a complete outage with no new blocks containing payloads produced in the last 12 hours, only empty blocks. According to pk910, the network had been running on a single Prysm node (prysm-geth-3) plus bootnode after other nodes fell out of sync following "gloas activation," but both remaining nodes are now experiencing "context deadline exceeded" errors without clear block production error messages.

In response to the outage, terencechain suggested either restarting from scratch or launching a new devnet-2 with a better coordinated genesis date when more clients are ready. They noted that devnet-1's launch during EthCC and the holidays made it difficult to provide proper attention and debugging resources, suggesting timing was a contributing factor to the current issues.

No definitive decision was reached in this discussion, leaving open questions about whether to attempt recovery of devnet-1 or proceed with a fresh devnet-2 deployment.

## Participants

- pk910
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-07T16:59:59.057000+00:00] pk910: Heya,
It looks like there's a full outage of epbs-devnet-1 now.
No new block with payload since 12h - only some empty ones.

I'm not fully up to date on what was going on before,  but I think the last status was that there is one prysm node (prysm-geth-3) + bootnode running the chain as all other nodes fall off sync right after gloas activation.
Both nodes are now throwing context deadline exceeded errors,  but nothing obvious bad like a block production error message :/
[2026-04-07T17:04:22.890000+00:00] terencechain: Does it make sense to restart from scratch, or even do a devnet2 with a more agreed-on genesis date when majority of clients are ready? Devnet1 happened during EthCC and then the holidays, so it was pretty hard to give it full attention, let alone spend time debugging
```

</details>
