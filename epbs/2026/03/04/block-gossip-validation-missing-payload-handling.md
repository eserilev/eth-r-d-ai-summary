# Block Analysis and Payload Rejection Investigation

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers on investigating payload rejections in an Ethereum devnet involving multiple client implementations. Potuz identified that Prysm nodes were incorrectly rejecting payloads from Teku instead of ignoring them, which caused Lodestar and Lighthouse nodes to go offline. The root cause was traced to a missing commit (https://github.com/OffchainLabs/prysm/pull/16461) that should have changed the behavior from "reject" to "ignore" when validating payloads from blocks on different branches. The issue occurred because Teku was operating on a separate, longer branch with different lookahead parameters.

The team attempted to retrieve the specific problematic block (root `0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1`) from various nodes and monitoring systems but were unsuccessful, as the block appears to have been orphaned and is no longer available through standard APIs. Potuz confirmed the fix exists in the develop branch and planned to push it to the devnet branch. The discussion ended with uncertainty about recovery options, as the network's ability to restart depends on whether Lodestar or Lighthouse support serving payloads by range.

## Participants

- parithosh
- pk910
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T17:45:01.616000+00:00] potuz: Does anyone have the block for 318?
[2026-03-04T17:45:34.380000+00:00] potuz: that's the first time that we reject from Lighthouse, it all goes down to block 281 from Teku
[2026-03-04T17:58:15.873000+00:00] potuz: Can anyone give me the Teku proposed block at slot 349? is the root of that block 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1?
[2026-03-04T17:58:37.400000+00:00] parithosh: Yeah lemme fetch it from the api
[2026-03-04T18:00:54.833000+00:00] potuz: I think that's the root, and I know why we are downscoring pers
[2026-03-04T18:01:35.189000+00:00] potuz: for some unfathomable reason, the following commit did not make it to the branch: https://github.com/OffchainLabs/prysm/pull/16461
It causes a reject instead of an ignore
[2026-03-04T18:02:24.169000+00:00] potuz: when Lodestar and Lighthouse started regenerating states to validate the payloads from Teku, we rejected the payloads instead of ignoring them
[2026-03-04T18:02:38.915000+00:00] potuz: Teku was in a very long branch and was proposing with a different lookahead
[2026-03-04T18:02:50.243000+00:00] parithosh: is /eth/v2/beacon/blocks/349 enough for you?
[2026-03-04T18:03:22.065000+00:00] potuz: better give me 355 from Teku
[2026-03-04T18:03:26.237000+00:00] potuz: which has the parent root there
[2026-03-04T18:03:40.739000+00:00] potuz: if the parent root is `0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1`that's all I need
[2026-03-04T18:04:03.957000+00:00] potuz: ok Teku geth 1
[2026-03-04T18:04:14.126000+00:00] parithosh: `"parent_root": "0x3072a119a6f2beb1be30f2be38d9b3600a0e0624636d4ea5723c4808ceb0c9fd",`
[2026-03-04T18:04:19.303000+00:00] potuz: lol
[2026-03-04T18:04:35.373000+00:00] parithosh: 
[2026-03-04T18:04:37.197000+00:00] parithosh: full msg
[2026-03-04T18:04:45.931000+00:00] parithosh: so this is for `/eth/v2/beacon/blocks/355`
[2026-03-04T18:04:54.199000+00:00] potuz: no this is on a node in the other branch
[2026-03-04T18:05:01.645000+00:00] potuz: there are two blocks in each slot now
[2026-03-04T18:05:23.228000+00:00] parithosh: ah, but this is on teku-geth-1
[2026-03-04T18:05:26.398000+00:00] parithosh: should i search by root?
[2026-03-04T18:05:27.003000+00:00] potuz: You are looking on https://dora.epbs-devnet-0.ethpandaops.io/slot/355
[2026-03-04T18:05:47.685000+00:00] potuz: yeah I want the block 0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1 if it exists
[2026-03-04T18:06:05.512000+00:00] potuz: we killed Lodestar and Lightouse after trying to serve us that payload
[2026-03-04T18:06:26.864000+00:00] parithosh: ```
curl -s -H "Accept: application/json" http://localhost:5052/eth/v2/beacon/blocks/0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1
{"code":404,"message":"Not found"}
```
[2026-03-04T18:07:01.657000+00:00] potuz: yeah I think that Teku node was tried to be resynced perhaps
[2026-03-04T18:08:06.214000+00:00] potuz: anyway I
[2026-03-04T18:08:12.325000+00:00] parithosh: its created and been up for 2h
[2026-03-04T18:08:15.108000+00:00] parithosh: so maybe around that time?
[2026-03-04T18:08:40.177000+00:00] parithosh: <@808969530608451584> can we get it from dora? cause it has it listed as orphaned so maybe its in the db?
[2026-03-04T18:08:46.824000+00:00] parithosh: i checked tracoor and we only have stuff from slot 396
[2026-03-04T18:10:19.181000+00:00] potuz: Dora has the block listed but it doesn't give it to me.
[2026-03-04T18:10:28.512000+00:00] potuz: Perhaps Lodestar has it do you have access to Lodesar?
[2026-03-04T18:10:33.115000+00:00] potuz: they serve us the payload
[2026-03-04T18:10:35.026000+00:00] potuz: that's why
[2026-03-04T18:11:23.631000+00:00] pk910: yea,  I think dora didn't get that block :/
We had some problems with it earlier.. It wasn't running when the block was proposed
[2026-03-04T18:12:05.372000+00:00] potuz: Anyway I'm pretty sure that's the reason, our pending queue validates the signature and it failed verification because it was on a separate branch and we should have ignored it instead
[2026-03-04T18:12:13.640000+00:00] potuz: I thought this commit was already in that branch
[2026-03-04T18:12:16.825000+00:00] potuz: it's in develop now
[2026-03-04T18:12:24.046000+00:00] potuz: will push it to the devnet branch in a bit
[2026-03-04T18:13:03.948000+00:00] parithosh: checking lodestar
[2026-03-04T18:13:36.208000+00:00] parithosh: on lodestar: `curl -s -H "Accept: application/json" http://localhost:5052/eth/v2/beacon/blocks/0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1
{"code":404,"message":"Block not found for id '0x1f9fac30bf2141731f69309147aa0cd965f8c59d2ac8b21cedbc08e2f158d5a1'"}%`
[2026-03-04T18:15:13.629000+00:00] potuz: crap
[2026-03-04T18:15:26.596000+00:00] potuz: perhaps logs from these clients
[2026-03-04T18:15:30.647000+00:00] potuz: do I have access to those nodes?
[2026-03-04T18:16:06.711000+00:00] potuz: ```devops@lighthouse-geth-2.srv.epbs-devnet-0.ethpandaops.io: Permission denied (publickey).```
[2026-03-04T18:16:13.586000+00:00] potuz: 🙁
[2026-03-04T18:19:44.390000+00:00] potuz: if either Lodestar or Lighthouse supports serving payloads by range, we will be able to catch up restarting
[2026-03-04T18:19:53.688000+00:00] potuz: otherwise we're kinda screwed now
```

</details>
