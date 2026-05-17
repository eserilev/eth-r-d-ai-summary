# Post-Gloas checkpoint sync state expectations discussion

---

## 2026-04-01 (epbs)

The discussion centers on the expected state format for checkpoint sync servers following the Gloas update. pawandhananjay raised confusion about whether checkpoint sync servers should return a pending state or could also return a full state when the finalized slot is a skip slot.

barnabasbusa directed pawandhananjay to previous discussion threads for context, while potuz clarified that the state should always be "post CL block." When asked for confirmation, potuz confirmed that the finalized block refers to the finalized block, meaning the finalized state returned from checkpoint servers would always be the pending state post-Gloas.

The discussion appears to reach consensus that checkpoint sync servers should consistently return pending states after Gloas implementation, regardless of whether the finalized slot is a skip slot or not.

**Participants:** barnabasbusa, pawandhananjay, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-01T20:45:43.828000+00:00] pawandhananjay: i'm confused about which state to expect from the checkpoint sync server post gloas.  Should we always expect a pending state or can we also get a full state when the finalized slot is a skip slot.
[2026-04-01T21:57:02.359000+00:00] barnabasbusa: <@491624610215886849> welcome to the party. https://discord.com/channels/595666850260713488/874767108809031740/1479432783792439388 please read from here 😄
[2026-04-01T21:57:28.282000+00:00] barnabasbusa: TLDR: https://discord.com/channels/595666850260713488/874767108809031740/1479434870391312404
[2026-04-01T22:55:17.318000+00:00] potuz: it should be always post CL block
[2026-04-01T23:02:53.921000+00:00] pawandhananjay: the block here is the finalized block? so the finalized state returned from the checkpoint server would always be the pending state?
[2026-04-01T23:10:16.904000+00:00] potuz: yes
```

</details>

---

## 2026-04-03 (epbs)

The discussion centers around implementation challenges with checkpoint sync when dealing with skipped slots. Lighthouse (LH) is experiencing difficulties advancing pending state from the previous epoch into the finalized epoch because they need the payload data for skipped slots. Prysm handles this by requesting payloads for the slot range of the finalized checkpoint and blocks from slot+1, then deciding what to apply later using range requests starting from the checkpoint state obtained from the server.

Sproul from Lighthouse identifies that their implementation requires a finalized state in the database to start the network layer, and they currently enforce epoch-alignment constraints. They plan to investigate loosening the requirement that the starting state must be epoch-aligned. Potuz clarifies that the checkpoint state is epoch-aligned except for the payload, questioning whether Lighthouse assumes the state advanced to the epoch is canonical, noting this same issue affects other areas of their implementation.

The discussion reveals different architectural approaches between clients, with nflaig reporting ongoing checkpoint sync difficulties and peer connectivity issues. No definitive solution is reached, leaving Lighthouse to explore relaxing their epoch-alignment constraints as a potential path forward.

**Participants:** nflaig, potuz, sproul, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-03T10:53:25.170000+00:00] sproul: agree

for LH we are struggling to do checkpoint sync at skipped slots because we can no longer advance the pending state from the prev epoch into the finalized epoch (need the payload)

how does Prysm handle this?
[2026-04-03T10:54:08.475000+00:00] potuz: We request Payloads for the slot range of the finalized checkpoint and blocks from slot+1
[2026-04-03T10:54:16.837000+00:00] potuz: And decide later what to apply
[2026-04-03T11:29:21.437000+00:00] sproul: you fetch this from the checkpoint server via HTTP?
[2026-04-03T11:41:49.583000+00:00] nflaig: that seems to be still the case, I am not able to checkpoint sync, also very rough to get any peers
[2026-04-03T12:05:09.627000+00:00] potuz: No, we start requesting by range
[2026-04-03T12:05:57.371000+00:00] potuz: Just the same as now: you get the state from the server and we start requesting the range data starting from it
[2026-04-03T12:10:58.855000+00:00] sproul: Hmm ok, we don't really have a way of doing this easily. We need some finalized state in the DB in order to start the network layer. I'll see if we can loosen the constraint that the starting state must be epoch-aligned.
[2026-04-03T12:12:38.562000+00:00] potuz: Hmm I don't understand, the checkpoint state is epoch aligned, except the Payload if it's there, is this the problem? You assume that the state advanced to the epoch is canonical?
[2026-04-03T12:13:16.495000+00:00] potuz: That sameb problem is biting us in other areas
[2026-04-03T14:52:24.142000+00:00] terencechain: my bad, i forgot, and we dont track this as the skipped slots attestations never get counted on chain right? we could if we want to but it seems like a lot of complexity for a minimal gain
```

</details>

---

## 2026-04-06 (epbs)

The discussion centers on handling complexity in checkpoint sync implementations, specifically around payload envelope fetching. 0xunclebill explains that without pre-emptively fetching payload envelopes from the checkpointz server, their implementation requires special handling during range sync to import the envelope at the checkpoint slot, suggesting it would be simpler to have the checkpointz server support `GET eth/v1/beacon/payload_envelopes/{block_id}`.

Potuz suggests an alternative approach using offset requests during range sync (slot+N for payloads, slot+1+N for blocks), but 0xunclebill clarifies their implementation uses a "batches" concept that groups ByRange requests for blocks, columns, and envelopes by epoch with batch IDs for tracking. Their coupling logic prevents importing incomplete data sets (columns without blocks, envelopes without columns/blocks, etc.).

While 0xunclebill acknowledges they could special-case the first envelope, they conclude it would be much simpler to fetch the envelope from the checkpoint server before initiating range sync. No explicit consensus was reached, leaving the implementation approach as an open decision point.

**Participants:** 0xunclebill, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-06T05:21:29.860000+00:00] 0xunclebill: btw regarding checkpoint sync complexity, if we dont pre-emptively fetch the payload envelope from the checkpointz server we need special handling in range sync for importing the envelope at the checkpoint slot. its easier for us to just have checkpointz server enable `GET eth/v1/beacon/payload_envelopes/{block_id}`
[2026-04-06T09:32:58.954000+00:00] potuz: When you range sync anyway you're making different requests aren't you? You can always request slot, slot+N for Payloads and slot+1, slot+1+N for blocks and works everytime
[2026-04-06T10:11:19.609000+00:00] 0xunclebill: we have this concept of “batches” in our range sync impl which is a grouping of ByRange requests for blocks columns and envelopes for a given epoch. The batch ID allows us to keep track of all the different requests and we couple all the responses for a given batch id and import everything together
[2026-04-06T10:13:17.303000+00:00] 0xunclebill: The coupling logic also prevents us from importing columns without a block. or envelopes without columns and block. etc 

anyways we can special case the first envelope but it becomes much simpler if we just fetch the envelope from the checkpoint server before letting range sync take over
```

</details>

