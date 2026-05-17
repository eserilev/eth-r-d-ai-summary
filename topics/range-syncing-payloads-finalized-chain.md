# Range Syncing Without Payloads for Finalized Chain

---

## 2026-04-12 (epbs)

The discussion centers around optimizing range syncing for the finalized portion of the Ethereum chain by potentially avoiding payload verification. Potuz raises the question of whether it's safe to sync beacon blocks up to the finalized checkpoint without sending `newPayload` or `forkchoiceUpdated` (FCU) calls to the execution layer, noting that most execution layer clients likely perform snap sync anyway. The concern is whether nodes could follow an invalid chain if they sync without payload validation, especially given that execution clients may snapshot sync from potentially invalid states.

Sproul confirms that Lighthouse already implements this optimization - they don't send `newPayload` for finalized blocks during range syncing and only start sending payloads once they reach the unfinalized portion of the chain. However, the discussion reveals a technical challenge: while Lighthouse verifies RLP block hashes, other clients don't implement RLP hash verification (which is why they currently send `newPayload` calls). This creates uncertainty about whether payload-by-range calls can be completely avoided for the finalized section.

The key open question remains whether there's a security risk of syncing through invalid finalized blocks without payload verification. Potuz notes that if block hash verification is necessary regardless, then payload-by-range calls might still be required for the finalized section. The discussion concludes with a suggestion to bring this topic to the next ACDT meeting where execution layer developers can provide additional input on the safety implications.

**Participants:** nflaig, potuz, sproul

<details>
<summary>Raw messages</summary>

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

---

## 2026-04-20 (epbs)

potuz initiated a discussion about removing "payload by range" functionality entirely from the Ethereum specification, arguing it appears useless based on findings from another Discord thread. The conversation explored whether execution payloads are needed at all, even for backfill operations, with nflaig expressing concern about API consumers querying execution payload envelope data through beacon chain APIs.

The group reached a tentative consensus that payload envelopes could potentially be pruned completely, with potuz suggesting that "payload by root" RPC calls should remain only to stay current with the head of the chain. Key technical considerations include treating blocks synced without their parent payload as optimistic (requiring updates to the optimistic sync specification), keeping unfinalized payloads on clients to serve by root requests, but allowing finalized payloads to be pruned and never served by range.

The discussion represents a significant potential simplification to the Ethereum specification, though potuz acknowledged he "may be terribly wrong" about removing envelopes entirely, indicating this proposal requires further validation and consensus from the broader development community.

**Participants:** 0xunclebill, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-20T15:23:19.139000+00:00] potuz: From what I'm gathering in https://discord.com/channels/595666850260713488/1493309049708744756 it seems to me that we can remove payload by range completely from the spec. It seems useless to me unless I'm missing something
[2026-04-20T15:24:59.933000+00:00] nflaig: so for backfill you would rely on by root?
[2026-04-20T15:25:19.902000+00:00] potuz: I don't think we need the Payloads at all
[2026-04-20T15:25:26.919000+00:00] potuz: Even for backfill
[2026-04-20T15:25:50.645000+00:00] nflaig: my only worry with that would be for api consumers that wanna query `GET /eth/v1/beacon/execution_payload_envelope/{block_id}` chain data
[2026-04-20T15:26:13.966000+00:00] nflaig: but maybe that api shouldn't exist?
[2026-04-20T15:26:42.515000+00:00] potuz: We do need RPC by root to stay up to head
[2026-04-20T15:26:53.560000+00:00] potuz: But I don't think we need by range at all
[2026-04-20T15:28:28.745000+00:00] 0xunclebill: theres no reason for keeping envelopes around at all? we can prune them completely as well?
[2026-04-20T15:34:30.254000+00:00] potuz: I think we can. I may be terribly wrong but I don't see why we need envelopes at all. The only caveat is that a block that has been synced without it's parent Payload needs to be treated as optimistic
[2026-04-20T15:34:45.105000+00:00] potuz: We should change the optimistic sync spec to reflect this
[2026-04-20T15:35:15.610000+00:00] potuz: And unfinalized payloads do need to be kept on clients to serve by root
[2026-04-20T15:35:59.685000+00:00] potuz: But I think we can prune finalized ones and never ever serve any, finalized or not, by range
```

</details>

---

## 2026-04-22 (epbs)

Potuz introduced a new EIP (PR #11557) that would allow Consensus Layer (CL) clients to sync without requesting execution payloads. The proposal suggests that finalized envelopes can be pruned entirely and that even unfinalized sections can sync without payload requests, potentially improving sync efficiency.

When asked about data availability implications, particularly regarding data columns that nodes need to custody, potuz confirmed that the data availability (DA) process remains unchanged since KZG commitments are still part of the bid structure. The discussion also touched on some administrative matters, including dealing with spam reviews and a request for EIP editors to assign a proper number to the proposal so it can be discussed in the upcoming ACDE meeting.

The technical feasibility appears sound based on the brief exchange, though the proposal awaits broader review and formal numbering before being discussed in core developer meetings.

**Participants:** barnabasbusa, jtraglia, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-22T20:20:48.949000+00:00] potuz: Added this EIP to allow CL clients to sync without even requesting payloads, I think we can (and therefore should) prune all finalized envelopes and even on the unfinalized section we can sync without requesting payloads. https://github.com/ethereum/EIPs/pull/11557
[2026-04-22T20:21:05.104000+00:00] potuz: Could you guys please take a look? also awaiting for authorization to add authors 🙂
[2026-04-22T20:24:17.171000+00:00] nflaig: what about data columns that the node needs to custody? I guess that is fine since the kzg commitments are part of the bid
[2026-04-22T20:25:03.724000+00:00] potuz: yeah DA doesn't change at all
[2026-04-22T21:31:07.782000+00:00] potuz: This guy Tony is a faster reviewer than all of us
[2026-04-22T21:32:38.219000+00:00] jtraglia: Will block them from the ethereum org.
[2026-04-22T21:33:46.080000+00:00] jtraglia: 
[2026-04-22T21:33:56.828000+00:00] jtraglia: Cannot hide their comments though.
[2026-04-22T21:34:04.754000+00:00] barnabasbusa: he is calling for <@1126229231572094976>
[2026-04-22T21:35:23.670000+00:00] barnabasbusa: can an EIP editor pls assign it a proper number, so we can discuss it by its number tomorrow on acde? <@642887713246150666> <@792822129019584562>
[2026-04-22T23:39:59.589000+00:00] potuz: I swear the first time I got the email on my tty viewer I thought that. Given that <@1126229231572094976> was one of the first ones worried about the interaction with BAL it even made sense!
```

</details>

