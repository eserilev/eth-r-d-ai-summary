# Payload by Range Removal Discussion

**Channel:** epbs | **Date:** 2026-04-20

## Summary

Potuz proposed removing the "payload by range" functionality entirely from the Ethereum specification, arguing it appears useless based on discussions in another channel. The conversation explored whether execution payloads are needed at all, even for backfill operations, with nflaig raising concerns about API consumers who might query execution payload envelope data.

The discussion evolved toward potentially eliminating execution payload envelopes completely and allowing clients to prune them. Potuz suggested that blocks synced without their parent payload should be treated as optimistic, requiring updates to the optimistic sync specification. However, he noted that unfinalized payloads would still need to be retained by clients to serve "by root" requests.

The emerging consensus was that finalized payloads could be pruned and never served by range (finalized or not), while keeping "by root" RPC functionality to stay synchronized with the chain head. The discussion leaves open questions about the broader implications for API design and whether certain payload-related APIs should exist at all.

## Participants

- 0xunclebill
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
