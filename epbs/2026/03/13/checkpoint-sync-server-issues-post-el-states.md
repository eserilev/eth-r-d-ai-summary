# Checkpoint Sync Server Issues and Post-EL States

**Channel:** epbs | **Date:** 2026-03-13

## Summary

Potuz reported random failures during checkpoint sync, suspecting that servers were serving post-execution layer (EL) states instead of the expected pre-EL states. The issue manifested as checkpoint states having payloads already applied, which caused sync failures and wasted several hours of debugging time. Terencechain acknowledged and pushed a fix, while barnabasbusa temporarily removed Lodestar from the checkpoint sync server mix, leaving only Prysm and Lighthouse nodes serving checkpoints.

After the changes, Potuz confirmed successful syncing with proper states being received. However, barnabasbusa was unable to replicate the original issue when testing with a direct Lodestar endpoint. Potuz provided the technical details of his detection method, which checks if the latest execution payload bid block hash equals the latest block hash, indicating a post-EL state. He suspected the issue might occur when finalized checkpoint roots are not at slot 0, and noted that either Prysm or Lodestar could be the source of the problem.

The immediate issue appears resolved with the server configuration changes, but the root cause remains unclear. Potuz suggested further testing of Lodestar nodes in various scenarios, particularly when slot zero is missed, and mentioned plans to open an init-sync PR. The discussion highlights the need for more robust checkpoint sync validation and clearer state requirements across different client implementations.

## Participants

- barnabasbusa
- jtraglia
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-13T00:02:17.965000+00:00] potuz: <@412614104222531604> did we by any chance add another server that is serving post-EL states on checkpoint sync? I'm failing randomly because of this
[2026-03-13T00:16:34.444000+00:00] potuz: fow what *cough* I think is this
[2026-03-13T00:47:34.851000+00:00] potuz: It'd be nice to know what the checkpoint sync server is serving, I'm randomly failing to sync and I can't explain it other than the server is giving me full states
[2026-03-13T00:48:01.494000+00:00] potuz: Ah I can probably check locally and add a warning
[2026-03-13T00:56:30.080000+00:00] terencechain: ya sorry I'll fix it tonight, I keep forgetting... 🙁
[2026-03-13T01:08:00.549000+00:00] jtraglia: All good lol. Thanks!
[2026-03-13T01:15:50.789000+00:00] potuz: <@412614104222531604> ```[2026-03-12 22:13:29.13]  WARN checkpoint-sync: Checkpoint sync state has payload already applied```  Someone has screwed me over the last few hours
[2026-03-13T01:22:14.630000+00:00] potuz: yeah confirmed tha tI can sync fine when the checkpoint is reasonable and I can't when it's not, I should probably harden to either fail bluntly or sync from a full post-state, but we should not be serving these checkpoints on the devnet for now to have a stable testing environment. I wasted a few hours today chasing down my bugs and it was this that randomly picks from different client implementations I guess
[2026-03-13T03:16:08.185000+00:00] terencechain: just pushed 🙏
[2026-03-13T05:43:54.993000+00:00] barnabasbusa: only prysm and lodestar
[2026-03-13T05:44:39.762000+00:00] barnabasbusa: I’ll remove lodestar again from the mix
[2026-03-13T05:44:51.471000+00:00] barnabasbusa: based on initial findings it seemed ok
[2026-03-13T08:24:38.251000+00:00] potuz: No need to remove lodestar for all I know it could have been Prysm the faulty one. But if there's only one client serving I can test today when I wake up and check if that one client is serving the right state
[2026-03-13T09:03:15.748000+00:00] barnabasbusa: you can directly hit different beacon node’s api too, if you want to test specific nodes?
[2026-03-13T11:14:46.014000+00:00] potuz: what's the status now? I just synced three times in a row without trouble and with the right state received from the endpoint.
[2026-03-13T11:15:44.018000+00:00] barnabasbusa: i have removed lodestar
[2026-03-13T11:17:57.220000+00:00] potuz: ah, ok, perhaps we can query a lodestar node a few times and check if it returns the post-cl state in every situation (including if slot zero is missed which is what I suspected). I can do that later, but want to at least open the init-sync PR this morning
[2026-03-13T12:01:05.317000+00:00] barnabasbusa: I'll do a quick tests
[2026-03-13T12:52:23.549000+00:00] barnabasbusa: I can't replicate your issue potuz. 

I've been testing with 

```yaml
participants_matrix:
  el:
    - el_type: geth
      el_image: ethpandaops/geth:epbs-devnet-0
  cl:
    - cl_type: lighthouse
      cl_image: ethpandaops/lighthouse:epbs-devnet-0
      cl_extra_params:
        - --epochs-per-migration 99999
    - cl_type: lodestar
      cl_image: ethpandaops/lodestar:epbs-devnet-0
    - cl_type: prysm
      cl_image: ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync
network_params:
  network: epbs-devnet-0
checkpoint_sync_enabled: true
checkpoint_sync_url: https://beacon.epbs-devnet-0.ethpandaops.io/lodestar
```

And its been working
[2026-03-13T12:55:56.809000+00:00] barnabasbusa: if you wanna make sure you get served from a specific node, you can specify it in our dugtrio instance as shown above
[2026-03-13T13:45:23.861000+00:00] potuz: my check is rather trivial:
```
        if s.Version() >= version.Gloas {
                if full, err := s.IsParentBlockFull(); err == nil && full {
                        log.Warn("Checkpoint sync state has payload already applied")
                }
```
Where 
```
func (b *BeaconState) IsParentBlockFull() (bool, error) {
        if b.version < version.Gloas {
                return false, errNotSupported("IsParentBlockFull", b.version)
        }

        b.lock.RLock()
        defer b.lock.RUnlock()

        if b.latestExecutionPayloadBid == nil {
                return false, nil
        }

        return bytes.Equal(b.latestExecutionPayloadBid.BlockHash, b.latestBlockHash), nil
}
```

So unless I'm completely crazy, that log shows that the returned state had the latest block hash equal to the latest bid blockhash.
[2026-03-13T13:45:43.007000+00:00] potuz: I suspect I may have caught a situation where the finalized checkpoint root was not slot 0
[2026-03-13T13:47:08.982000+00:00] potuz: And it may even be Prysm, if the stateByRoot picks up some epoch boundary cache in which we cache the root instead of the hash to advance across the boundary.  cc <@1057691141237125190> that implemented the endpoint
```

</details>
