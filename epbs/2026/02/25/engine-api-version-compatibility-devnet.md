# Engine API version compatibility issues and devnet configuration

**Channel:** epbs | **Date:** 2026-02-25

## Summary

Stefan encountered a compatibility issue while testing Kurtosis interop with Lighthouse and Geth, where Teku was calling `engine_forkchoiceUpdatedV4` but Geth only supported V3. The discussion revealed that while V4 is technically correct according to the spec, most clients (Lighthouse, Lodestar, Nimbus) were currently using V3 to maintain compatibility with the existing Geth implementation.

The team debated whether to update Geth to support V4 or keep all clients aligned on V3 for the current devnet. Marius pointed out that using V4 now would create complications later, as the eventual V4 implementation for BAL (Block Auction List) devnets would have different formatting requirements, forcing clients to support two incompatible versions of V4.

The consensus reached was to stick with `engine_forkchoiceUpdatedV3` for the current EPBS devnet 0 scope and only migrate to V4 when the merged devnet is implemented. This approach avoids creating technical debt and ensures all clients remain compatible without supporting multiple variations of the same API version.

## Participants

- barnabasbusa
- mariusvanderwijden
- nflaig
- stefanbratanov
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-25T13:26:06.596000+00:00] stefanbratanov: <@412614104222531604>  I get this from geth when trying kurtosis interop with lighthouse, any idea?
```
java.util.concurrent.CompletionException: tech.pegasys.teku.ethereum.executionclient.response.InvalidRemoteResponseException: Invalid remote response from the execution client: JSON-RPC error: Method not found (-32601): the method engine_forkchoiceUpdatedV4 does not exist/is not available
```
[2026-02-25T13:28:43.010000+00:00] barnabasbusa: are you running the latest geth image for sure?
[2026-02-25T13:28:49.784000+00:00] barnabasbusa: can you make sure you run with --image-download always
[2026-02-25T13:31:25.304000+00:00] stefanbratanov: ```
ethpandaops/geth:epbs-devnet-0 
```

Let me try  `--image-download always` , do I pass this to the config or to `run --enclave`
[2026-02-25T13:40:58.751000+00:00] stefanbratanov: Hmm seeing this https://github.com/ethereum/go-ethereum/commit/1f3041824fcdcd646eeaf59134b750ace21c2405 it says `Update engine_forkchoiceUpdatedV3 to accept Amsterdam payloads` but from the engine api it is engine_forkchoiceUpdatedV4
[2026-02-25T14:10:54.711000+00:00] barnabasbusa: lh,lodestar both used `engine_forkchoiceUpdatedV3`
[2026-02-25T14:11:27.880000+00:00] barnabasbusa: not saying they are right lol
[2026-02-25T14:11:34.327000+00:00] barnabasbusa: Let me see if we need to add v4
[2026-02-25T14:11:36.128000+00:00] stefanbratanov: Ok. let me try with engine_forkchoiceUpdateV3, but technically V4 is per spec
[2026-02-25T14:11:46.958000+00:00] nflaig: I did whatever made geth happy 😄
[2026-02-25T14:11:51.906000+00:00] barnabasbusa: cc <@586161934425128960> <@358120958726373381>
[2026-02-25T14:17:00.039000+00:00] barnabasbusa: Looking at the spec indeed we should have v4
[2026-02-25T14:17:33.718000+00:00] nflaig: but there are no changes required (for now) in epbs devnet 0
[2026-02-25T14:20:40.943000+00:00] barnabasbusa: https://github.com/ethereum/go-ethereum/pull/33892
[2026-02-25T14:20:55.229000+00:00] barnabasbusa: This will break lodestar and lighthouse and nimbus most likely
[2026-02-25T14:21:24.002000+00:00] barnabasbusa: let me know if you guys want it merged in
[2026-02-25T14:24:11.296000+00:00] stefanbratanov: I am fine with both, easy to change it for Teku, but probably long term better all to align
[2026-02-25T14:24:58.075000+00:00] tersec: we can adjust
[2026-02-25T14:25:10.208000+00:00] barnabasbusa: <@654267572107083777> what are you using now ?
[2026-02-25T14:25:12.838000+00:00] barnabasbusa: v3?
[2026-02-25T14:25:24.987000+00:00] tersec: V3. but we actually had V4 first until finding it didn't work
[2026-02-25T14:25:28.839000+00:00] tersec: it's not a major change either way
[2026-02-25T14:26:02.779000+00:00] barnabasbusa: I'd actually then say maybe we stick to v3 for devnet 0 scope
[2026-02-25T14:26:10.240000+00:00] barnabasbusa: and then for devnet 1 we bump to v4
[2026-02-25T14:27:04.008000+00:00] tersec: ok, ideally as long as it doesn't create an overhang where both variations has to be supported
[2026-02-25T14:29:10.823000+00:00] mariusvanderwijden: I think its better to use v3, because v4 will have changes to the format for BALs
[2026-02-25T14:29:39.394000+00:00] mariusvanderwijden: So clients would need to support two versions of v4, one for the epbs devnets and one for the bal devnets
[2026-02-25T14:29:43.281000+00:00] barnabasbusa: ok, so you suggest we do v4 only when we do merged devnet?
[2026-02-25T14:30:06.590000+00:00] mariusvanderwijden: I would say so, yes
[2026-02-25T14:30:36.125000+00:00] barnabasbusa: would that work for everyone <@&1417820113981145228> ?
[2026-02-25T14:31:01.981000+00:00] tersec: sure, it's fake now either way, fake v3 or fake v4
```

</details>
