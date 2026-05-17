# Range Syncing Without Payloads for Finalized Blocks

**Channel:** epbs | **Date:** 2026-04-12

## Summary

This discussion centers around optimizing Ethereum's range syncing process by potentially avoiding payload transmission for finalized blocks. The conversation was sparked by nflaig's proposal and explores whether clients need to send `newPayload` and `forkchoiceUpdated` (FCU) calls during range syncing up to the finalized checkpoint.

Lighthouse's implementation provides a practical reference point - they successfully avoid sending `newPayload` for finalized blocks during range syncing, only starting payload transmission once they reach the unfinalized portion of the chain. This approach allows execution layer (EL) clients to sync their own state independently. However, a technical concern emerges around block hash verification: while some clients verify RLP block hashes directly, others (like Potuz's client) rely on `newPayload` calls for this validation, making it difficult to completely eliminate payload-by-range calls for finalized sections.

The discussion concludes with an unresolved question about the security implications of syncing through potentially invalid finalized blocks when payloads aren't transmitted, particularly given that EL clients often use snapshot syncing rather than full chain validation. The participants agree to continue this discussion in the next ACDT meeting where execution layer developers can provide additional input on the feasibility and safety of this optimization.

## Participants

- nflaig
- potuz
- sproul

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-12T10:50:56.130000+00:00] nflaig: I think they just return syncing, is that relevant to my proposal or just a general question?
[2026-04-12T10:59:35.894000+00:00] potuz: Thanks, I'm just thinking on all the paths we need to remove because of your proposal, we may as well clean also pre-existing paths that I am pretty sure are wrong in our range syncing. Do you know if they get the blocks on their own. I ask cause we send new payloads chained when range syncing. This is because we do not implement RLP to check the hash. I believe we should not do this when syncing up to the finalized epoch and we shouldn't send neither FCU nor new payloads before hitting the finalized checkpoint. 

Your PR allows us to sync blocks again without the payload at all, I am uncertain of what the danger is of syncing without payloads up until the finalized checkpoint. As soon as we send the first FCU/New Payload the engine would be optimistic and eventually explicitly return an INVALID if any of the missing payloads was actually invalid is this correct?

I mean, is there a risk that a node would follow an invalid chain by syncing without sending the payloads? I am not entirely sure how EL clients sync through this, but I suspect most would do a snap sync and start taking blocks and forward sync from that snapshot, it may happen that the snapshot is already based on an invalid state and then they take the next blocks happily as long as they are valid correct? 

Anyway, not related to your PR, just thinking if when doing range syncing up to the finalized checkpoint we should do payload by range requests at all or not.
[2026-04-12T11:37:39.951000+00:00] nflaig: was discussing exactly that with <@534934855113506836> two days ago, it seems fine to me to not do payload by range, but probably something EL client should chime in as well
[2026-04-12T11:41:43.291000+00:00] potuz: In the not finalized case I believe we need to do by range for sure. My question is mostly about the finalized section
[2026-04-12T11:42:00.318000+00:00] potuz: Since anyway most people will checkpoint sync
[2026-04-12T11:43:21.792000+00:00] nflaig: yes I think we can just sync beacon blocks up to finalized checkpoint and avoid any new_payload or FCU calls
[2026-04-12T23:18:05.814000+00:00] sproul: Lighthouse does not send newPayload for the finalized chain while range syncing, and indeed it works fine, the ELs sync their own state once we get to the head
[2026-04-12T23:21:58.210000+00:00] potuz: Do you send them as soon as you reach the finalized checkpoint and then continue sending them in the unfinalized part?
[2026-04-12T23:23:24.890000+00:00] sproul: there are ones we never send (the finalized ones), and once we reach the unfinalized portion of chain we start sending the ones from that point forward to the head
[2026-04-12T23:23:56.093000+00:00] sproul: and we send fcU all the time, but the EL mostly complains that it doesn't know the block, so not sure if this is necessary either
[2026-04-12T23:24:03.611000+00:00] sproul: but the EL will also complain if you don't tell it anything 😛
[2026-04-12T23:27:22.541000+00:00] potuz: Perfect thanks, we should do this as well I believe. Since we need to strip this anyway from our range syncing if we go with Nico's PR, I think removing this could come for free there
[2026-04-12T23:43:50.285000+00:00] potuz: I would still like to know if there's a danger the client may sync through an invalid and finalized block this way though
[2026-04-12T23:47:36.936000+00:00] sproul: we verify the RLP block hash, so I think that covers it
[2026-04-12T23:48:57.501000+00:00] sproul: hmm, maybe not actually if the EL snapshot syncs and doesn't validate the full EL chain
[2026-04-12T23:51:07.813000+00:00] potuz: Yeah we don't 🙂 that's why we send new Payload
[2026-04-12T23:53:23.924000+00:00] potuz: And the problem is that if verifying the Blockhash is necessary anyway, then we can't avoid the payload by range call in the finalized section at all for any client.
[2026-04-12T23:54:40.953000+00:00] potuz: Perhaps this discussion can be had tomorrow in ACDT as well that some El devs will be there
```

</details>
