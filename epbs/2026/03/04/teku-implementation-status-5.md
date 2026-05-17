# Missing Teku Block Investigation and Validation

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers on investigating a missing Teku block that caused a long fork, specifically block `0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1` from slot 281. Potuz wanted to determine if this block was valid and why peers weren't serving payloads from that branch to enable proper syncing. When queried across all client implementations (Prysm, Teku, Lighthouse, and Lodestar), none of the nodes had the block in their databases, returning 404 errors.

Through investigation with 0xunclebill's help, they discovered that the Teku block at slot 281 was built on top of an old block from slot 251 (parent root `0x38fa552e74cbf6b33663214bc6ba661b062d0c80dfdfc5f7ddb62be6af69f691`), confirming Potuz's hypothesis. This revealed that the issue was caused by a combination of two different bugs in Prysm (plus an issue with Teku) that were already supposed to be fixed in that branch but inexplicably weren't deployed.

Potuz concluded the investigation by identifying this as both a processing bug and downscoring issue in Prysm, spending considerable time debugging due to misleading error messages. He published a post-mortem at https://www.potuz.net/posts/epbs-devnet-0/ documenting the findings.

## Participants

- 0xunclebill
- parithosh
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T18:26:55.595000+00:00] potuz: we can't sync if no one serves payloads
[2026-03-04T18:27:23.997000+00:00] potuz: there is only one thing that I am still left to understand though: I want to know if the block that causes the long fork by Teku is valid or not
[2026-03-04T18:27:33.400000+00:00] potuz: And no one seems to have it
[2026-03-04T18:27:34.074000+00:00] parithosh: seems like you won't find it anywhere, but it could ofc just not exist in logs but on the db.

```
curl -s -H 'Accept: application/json' http://localhost:5052/eth/v2/beacon/blocks/0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1
```
against all hosts:
```
bootnode-1 | CHANGED | rc=0 >>
{"code":404,"message":"Not found"}
prysm-geth-1 | CHANGED | rc=0 >>
{"message":"Block not found: block 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1 not found in db","code":404}
prysm-geth-3 | CHANGED | rc=0 >>
{"message":"Block not found: block 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1 not found in db","code":404}
teku-geth-1 | CHANGED | rc=0 >>
{"code":404,"message":"Not found"}
lighthouse-geth-2 | CHANGED | rc=0 >>
{"code":404,"message":"NOT_FOUND: beacon block with root 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1","stacktraces":[]}
lighthouse-geth-1 | CHANGED | rc=0 >>
{"code":404,"message":"NOT_FOUND: beacon block with root 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1","stacktraces":[]}
lodestar-geth-3 | CHANGED | rc=0 >>
{"code":404,"message":"Block not found for id '0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1'"}
lodestar-geth-1 | CHANGED | rc=0 >>
{"code":404,"message":"Block not found for id '0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1'"}
lodestar-geth-2 | CHANGED | rc=0 >>
{"code":404,"message":"Block not found for id '0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1'"}
lighthouse-geth-3 | CHANGED | rc=0 >>
{"code":404,"message":"NOT_FOUND: beacon block with root 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1","stacktraces":[]}
prysm-geth-2 | CHANGED | rc=0 >>
{"message":"Block not found: block 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1 not found in db","code":404}
```
[2026-03-04T18:27:38.541000+00:00] parithosh: No one has it
[2026-03-04T18:28:16.840000+00:00] potuz: yeah but after finalization perhaps it gets pruned, or even worse it may be invalid (as Prysm declares it)
[2026-03-04T18:28:24.129000+00:00] potuz: that's why I am after this
[2026-03-04T18:28:45.597000+00:00] potuz: I want to know if peers should have been serving us stuff from that other branch or not
[2026-03-04T18:28:52.456000+00:00] potuz: to begin with
[2026-03-04T18:30:00.524000+00:00] potuz: Ahh I know how to validate this, just a sec
[2026-03-04T18:49:51.616000+00:00] potuz: Ok, so I believe this was also a processing bug from Prysm in addition to the downscoring, I can't know for certain cause no one has that block, but I will have a post-mortem in a minute
[2026-03-04T19:51:19.539000+00:00] potuz: I would love to get Teku's block for that slot 281 cause I can't fully explain it unless it was building on something other than 224
[2026-03-04T19:58:49.743000+00:00] potuz: <@358120958726373381> could I ask you please to parse your logs to see if you processed the beacon block from teku at slot 281?
[2026-03-04T19:58:57.583000+00:00] potuz: and tell me what information you have?
[2026-03-04T20:00:46.013000+00:00] 0xunclebill: yessir
[2026-03-04T20:01:10.612000+00:00] potuz: Thanks so much, I want to know if it's based on 251
[2026-03-04T20:07:41.066000+00:00] 0xunclebill: looks like we imported both the block and payload from slot 281

```
Mar 04 15:28:11.072 INFO  New block received                            slot: 281, root: 0x1f7fe87ff8efeb5871492c69da8efa7229ef6ef278da875f3a0aeb2c7cdaedf1
Mar 04 15:28:11.140 INFO  Forwarding register validator request to connected builder  count: 200
Mar 04 15:28:11.145 WARN  Relay error when registering validator(s)     num_registrations: 200, error: ServerMessage(ErrorMessage { code: 502, message: "no successful relay response", stacktraces: [] })
Mar 04 15:28:11.145 WARN  Error processing HTTP API request             elapsed_ms: 14.227335, status: 500 Internal Server Error, path: /eth/v1/validator/register_validator, method: POST
Mar 04 15:28:11.636 INFO  New envelope received                         slot: 281, root: 0x1f7fe87ff8efeb5871492c69da8efa7229ef6ef278da875f3a0aeb2c7cdaedf1
Mar 04 15:28:11.641 INFO  Execution payload envelope imported           block_root: 0x1f7fe87ff8efeb5871492c69da8efa7229ef6ef278da875f3a0aeb2c7cdaedf1, block_slot: 281, source: gossip, block_source: gossip
```
[2026-03-04T20:08:10.304000+00:00] potuz: ah good root
[2026-03-04T20:08:24.819000+00:00] potuz: now what's the parent do you know?
[2026-03-04T20:08:49.556000+00:00] 0xunclebill: it should be `0x64f09aa9e5b61094a41edbc36643d077ba2a70f57567be80a1edf4ab7650f613`
[2026-03-04T20:08:55.684000+00:00] potuz: I need to know on top of which block is based
[2026-03-04T20:09:12.898000+00:00] potuz: where do you read this hash?
[2026-03-04T20:09:36.966000+00:00] 0xunclebill: actually thats not 100% accurate, that just logs from the previous slot. not necessarily what the block was built on
[2026-03-04T20:09:41.511000+00:00] 0xunclebill: one sec
[2026-03-04T20:09:50.936000+00:00] potuz: no surely 280 is what you say
[2026-03-04T20:10:01.212000+00:00] potuz: but Teku's block has to be built over some very old block
[2026-03-04T20:10:08.246000+00:00] potuz: I want to confirm it's 251
[2026-03-04T20:12:07.744000+00:00] potuz: <@363800010518822915> you'll want you kill yourself when you read my post-mortem
[2026-03-04T20:13:11.919000+00:00] 0xunclebill: this is the actual parent root `0x38fa552e74cbf6b33663214bc6ba661b062d0c80dfdfc5f7ddb62be6af69f691` that block was built on
[2026-03-04T20:13:47.121000+00:00] 0xunclebill: which is the block from slot 251
[2026-03-04T20:14:08.173000+00:00] potuz: ahhh I'm very good at guessing! thanks so much
[2026-03-04T20:14:09.796000+00:00] potuz: this confirms
[2026-03-04T20:14:42.001000+00:00] potuz: it was a very bizarre combination of two different bugs (plus Teku's) on Prysm that bizarrely are already fixed and **were supposed to be in that branch**
[2026-03-04T20:14:48.356000+00:00] potuz: inexplicably they werent
[2026-03-04T20:15:10.281000+00:00] potuz: spent hours because of a very misleading error message
[2026-03-04T20:34:48.021000+00:00] potuz: https://www.potuz.net/posts/epbs-devnet-0/
```

</details>
