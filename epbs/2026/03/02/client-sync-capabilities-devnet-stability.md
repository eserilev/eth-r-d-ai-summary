# Client Interoperability Testing and Issues

**Channel:** epbs | **Date:** 2026-03-02

## Summary

The discussion centers around interoperability testing for EPBS (Enshrined Proposer-Builder Separation) between different Ethereum clients. Potuz discovered that while Prysm and Lodestar interoperate successfully, adding Teku or Lighthouse causes significant issues, particularly with handling missed slots. The core problem appears to be incorrect attestation behavior - clients are attesting to index=0 during missed slots instead of attesting to the correct head, which kills chain liveness and triggers network splits.

Through testing, Potuz confirmed that Lodestar can handle missing payloads correctly, but all clients break on missing blocks because empty blocks win the vote due to improper attestation packing. The solution involves ensuring attestations point to the right head rather than defaulting to index=0, and Potuz noted that with proper attestation handling, the network can deal with missing blocks (humorously adding that as a physicist, this only works when the slot number is a multiple of 5).

Stefan from the Teku team acknowledged the issues and indicated that several PRs need to be merged over the coming days to fix Teku's attestation behavior. The consensus is that proper multi-client testing cannot proceed to devnet until these attestation issues are resolved, with Potuz temporarily limiting testing to Prysm-only or Prysm-Lodestar configurations.

## Participants

- jtraglia
- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-02T19:12:08.687000+00:00] potuz: Is Teku's image to test epbs already up on ethpandaops?
[2026-03-02T19:30:09.864000+00:00] jtraglia: Looks like it is, last updated 4 days ago though:
* https://hub.docker.com/layers/ethpandaops/teku/epbs-devnet-0/images/sha256-9e5d1435565695c80cc3ea5f0e206f71e67e73861f2ade798fbef1a8e01e16eb
[2026-03-02T19:32:53.503000+00:00] potuz: <@586161934425128960> Lodestar's interopping just fine with Prysm. As soon as I add Teku or LH everything goes to trash, so I'm guessing that No one can deal with missed slots. I'll be testing missed blocks/payloads only against Lodestar then
[2026-03-02T19:42:16.610000+00:00] jtraglia: <@755590043632140352> might be worth trying `consensys/teku:develop` too.
[2026-03-02T19:43:17.495000+00:00] potuz: Nah I'll be testing some bad cases with Lodestar, or even just Prysm, should be more than enough to find bugs
[2026-03-02T19:44:59.965000+00:00] potuz: Prysm-Lodestar Interop
[2026-03-02T21:04:52.361000+00:00] potuz: Hey <@&1417820113981145228> if you guys have an incomplete forkchoice that's fine, no PTC attestations that's also fine. But attestations should be packed correctly otherwise you will trigger a split. On missed slots you should attest to your right head, not index=0 cause that will kill the chain. If you want to hardcode something, hardcode 1 for previous slots attestations so that you don't kill liveness.
[2026-03-02T21:06:12.216000+00:00] potuz: I'm back to testing only Prysm now 😆 . Would love when you guys have an image that attests correctly to continue multiple client testing. I don't think we can go to devnet without this. I checked today that Lodestar can handle well payloads missing. But we all break on a missing block cause empty is winning the vote
[2026-03-02T21:08:49.070000+00:00] stefanbratanov: Thanks <@755590043632140352> for testing, I will be merging couple of PRs the next couple of days, otherwise Teku is not working properly, will message here when ready
[2026-03-02T21:29:11.966000+00:00] potuz: I confirmed that setting the attestations for the right head we can deal with missing blocks, without that PR we break even on a Prysm-only devnet
[2026-03-02T21:30:41.109000+00:00] potuz: Althouth a physicist may say that we can only deal with missing blocks when their slot is a multiple of 5, not willing to change my bad node.
```

</details>
