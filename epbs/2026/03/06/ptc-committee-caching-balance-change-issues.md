# PTC committee caching and balance change issues

**Channel:** epbs | **Date:** 2026-03-06

## Summary

The discussion centers around critical issues with PTC (Payload Timeliness Committee) committee caching that could cause chain splits when validator effective balances change. Potuz urgently advises against packing PTC attestations in blocks, especially for 0x02 validators, as balance changes could trigger a bug discovered by Nico that might split the network. The root problem appears to be that clients cache PTC committees but struggle to handle retroactive balance changes properly - nflaig notes that Teku had an initial bug (fixed, was actually in Lodestar) but acknowledges the spec provides no clear way to handle balance changes correctly.

Two main solutions are being considered: keeping each slot's PTC cached in the state (simpler implementation but "ugly from spec perspective") or storing two full epoch committees in the state (adding ~256KB but easier to specify). The current approach of precomputing committees for whole epochs and shifting them creates issues because previous epoch committees can change retroactively for the last slot. Potuz opened PR #4979 to address this, noting that keeping only one PTC has design drawbacks and relies on current behavior around empty slots and payload attestations.

The issue appears to be relatively rare in practice, and there are currently no 0x02 validators active, but it represents a significant consensus bug where blocks might be randomly rejected due to inconsistent committee caching across clients.

## Participants

- jtraglia
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-06T12:44:53.909000+00:00] potuz: Anyway, I'm happy that we have now reliable sync and checkpoint sync, so you can nuke pods for all I know all you want. But please please if we want to keep this chain going **do not pack PTC attestations in blocks!!!** specially if our vals ar 0x02
[2026-03-06T12:45:47.638000+00:00] potuz: their effective balance will be changing and we may be split by the issue that Nico found, will be working on alternatives to deal with this
[2026-03-06T13:10:51.662000+00:00] nflaig: the initial issue that we found with teku is fixed now, was a bug in lodestar but yeah if balance changes we are done probably, I don't know how to implement that with the current spec, there is no way to properly do this from what I can see in the client
[2026-03-06T13:17:54.181000+00:00] potuz: The simplest less invasive change on code I suspect it's keeping each slot's PTC in the state cached, since this is more or less what I suspect each client does: when we precompute the next slot state we just cache it. The problem is that this version is quite ugly from the spec perspective. Anyway will open that PR and perhaps another one with a 2 epoch lookahead (otherwise we still need to deal with the boundary issue)
[2026-03-06T13:20:30.031000+00:00] nflaig: so you don't precompute for the whole epoch? that's what we do because it's also needed for the ptc duties api right now, and we then shift current to previous epoch which is wrong because prev. can change retroactively for the last slot
[2026-03-06T13:24:22.581000+00:00] potuz: We cache the full committee but we seem to be computing by slot but that's in flux. I think it's absolutely fine to cache per epoch and store 2 of them in the state if people are happy with the extra 256KB in the state
[2026-03-06T13:24:29.579000+00:00] potuz: that's even easier to spec
[2026-03-06T13:26:00.206000+00:00] nflaig: yeah that's basically what we do right now but in the epoch cache attached to the state
[2026-03-06T14:09:00.198000+00:00] potuz: https://github.com/ethereum/consensus-specs/pull/4979
cc <@520034910149410861> 

The option of keeping only one PTC has several design drawbacks, although it's also simple. If we keep the latest PTC on the state, then we update it on process_block. It works but it relies heavily on current behavior that on empty slots there's no valid payload attestation that can be processed.
[2026-03-06T14:14:10.484000+00:00] potuz: If we keep the "last slot one" it's even uglier
[2026-03-06T21:04:30.631000+00:00] jtraglia: The spec bug related to this, right? https://github.com/ethereum/consensus-specs/pull/4979
[2026-03-06T21:04:39.341000+00:00] jtraglia: Or something else.
[2026-03-06T21:05:03.906000+00:00] nflaig: yes, it's that, we might reject a block randomly, I guess it's pretty rare, also no 0x02 validators
```

</details>
