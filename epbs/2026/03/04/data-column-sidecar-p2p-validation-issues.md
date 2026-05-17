# Peer Downscoring and P2P Issues Investigation

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around a P2P network isolation issue where Prysm nodes are aggressively downscoring and disconnecting from peers, leaving them unable to sync. Initial investigation showed Prysm disconnecting Lodestar and other client peers due to gossip scores falling below the -100 threshold (with scores as low as -907). Analysis revealed that Prysm rejected 393 peer connections, primarily due to gossip score violations (345 disconnections), with the node rejecting incoming gossip as invalid for reasons like "bid parent block hash mismatch" and "block root not seen."

The root cause appears to be Prysm's gossip scoring mechanism penalizing peers for sending what it considers invalid data, creating a feedback loop where the isolated node becomes increasingly disconnected from the network. Investigators using multiple analysis models confirmed that Prysm was classifying most connected peers as "bad gossip peers" and repeatedly disconnecting them. The team identified that Prysm may be inappropriately requesting blocks by root that it already built, potentially causing other clients to downscore it in return.

Key action items include improving Prysm's logging to include downscore reasons (similar to Lighthouse), investigating why Prysm is struggling to find new peers, and having the P2P team review the gossip scoring mechanism. The investigation is ongoing with potuz tracking rejection messages to identify the specific causes of invalid gossip detection.

## Participants

- barnabasbusa
- parithosh
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T16:15:29.550000+00:00] potuz: analyzing, we may be already isolated, Prysm is downscoring peers ```[2026-03-04 15:43:00.18] DEBUG p2p: Peer disconnected agent=lodestar/v1.40.0/ffbb1d1 direction=Inbound multiAddr=/ip
4/89.167.119.3/tcp/57152/p2p/16Uiu2HAmL7FgSMvsXC3maftW4hdoxpVDrMUpNhWt173RxBuDPyTh remainingActivePeers=2````
[2026-03-04T16:15:48.062000+00:00] potuz: ```[2026-03-04 15:43:05.10] DEBUG p2p: Initiate peer disconnection agent=lodestar/v1.40.0/ffbb1d1 direction=Inbound err
or=is bad peer no lock: gossip scorer: gossip score below threshold: got -907.045010 - threshold -100.000000 multiad
dr=/ip4/128.140.69.39/tcp/57498/p2p/16Uiu2HAkwrQaRVHAWah1Csf33wbUKRRghieAyBJkKJAZ2JYrHaXR remainingActivePeers=2````
[2026-03-04T16:16:21.056000+00:00] barnabasbusa: do you have a reason for the downscore ?
[2026-03-04T16:17:03.450000+00:00] potuz: not sure yet, looking, but yes, PRysm forks at this point cause it never gets a payload, even from Prysm ```[2026-03-04 15:43:11.07] DEBUG sync: Gossip message was ignored agent=Prysm/v7.1.3-rc.3/22328385a72bf5a1abdc75a58036
0850d61d2f37 error=parent payload not yet available gossipScore=32.72 multiaddress=/ip4/178.104.5.127/udp/13000/quic
-v1 peerID=16Uiu2HAmSjPemUuCRiB5gmQwscRGjapc6zaWmfpBjjyN8sSRazNC topic=/eth2/dbd4b481/beacon_block/ssz_snappy````
[2026-03-04T16:18:35.342000+00:00] barnabasbusa: I can yeet one node
[2026-03-04T16:18:40.592000+00:00] barnabasbusa: and try to sync to head and see if it works
[2026-03-04T16:18:48.138000+00:00] barnabasbusa: you have no peers
[2026-03-04T16:18:53.054000+00:00] barnabasbusa: prysm is isolated to prysm only
[2026-03-04T16:19:01.174000+00:00] barnabasbusa: you downscored everyone so you don't have anyone to provide you anything
[2026-03-04T16:19:06.385000+00:00] potuz: yes, we downscored them all
[2026-03-04T16:40:49.875000+00:00] potuz: could I check if it's not the other way around? perhaps other people are downscoring us? do you guys downscore if you receive many times a blocks by root for the same root? I am seeing we request a blockroot we ourselves built
[2026-03-04T16:43:07.323000+00:00] potuz: Perhaps we need to fix that we are requesting blocks by root
[2026-03-04T16:47:55.655000+00:00] potuz: I can't find any reason we are downscoring peers
[2026-03-04T16:48:03.796000+00:00] potuz: so I think others may be downscoring on blocks by root
[2026-03-04T16:48:16.507000+00:00] potuz: (we don't downscore by this I was told)
[2026-03-04T16:48:28.668000+00:00] potuz: so just in case I want to prevent requesting some blocks
[2026-03-04T16:49:38.769000+00:00] parithosh: ill try and see if i can find some downscoring info
[2026-03-04T16:52:00.569000+00:00] terencechain: <@755590043632140352>  i think these are the main reasons
we downscore on gossip errors 
and these are the errors and how many times we got them from peers
[2026-03-04T16:59:23.149000+00:00] terencechain: ```
You’re getting disconnected mainly because Prysm is classifying most connected peers as bad gossip peers, then repeatedly kicking them.
- I re-parsed the full log: 393 disconnections total; breakdown is 345 gossip-score kicks, 30 bad-responses kicks, 14 IP-colocation kicks, 4 other.
- The decisive error is visible at beacon_full.log:6513: gossip score below threshold: got -422... threshold -100; same for other peers at beacon_full.log:6520, and later at beacon_full.log:10797 with remainingActivePeers=0.
- Why scores tank: your node is rejecting incoming gossip as invalid (ex: bid parent block hash mismatch at beacon_full.log:5638 / beacon_full.log:5639, plus lots of “block root not seen” at beacon_full.log:5651). Rejected/invalid gossip drives peer score down until it crosses -100.
```
[2026-03-04T17:00:11.876000+00:00] terencechain: had two diff agents running two diff models to come to same conclusion 😂
[2026-03-04T17:00:32.469000+00:00] potuz: <@199561711278227457> you better deploy that image then, we may be right downscoring after all
[2026-03-04T17:00:50.597000+00:00] potuz: we'll have to talk to the p2p people about this, this is above my paygrade
[2026-03-04T17:06:48.235000+00:00] barnabasbusa: can you guys consider adding down score reason in the log message ?
[2026-03-04T17:06:54.667000+00:00] barnabasbusa: lh does it and it’s very useful
[2026-03-04T17:08:50.051000+00:00] potuz: following it, but I'm afraid that if we think peers are sending bad data we downscore them we will be in the same situation soon
[2026-03-04T17:08:50.174000+00:00] parithosh: ```
  4. Gossip scoring tanks Lighthouse peers - Because Prysm is stuck, it's not properly participating in gossip. Lighthouse nodes
  connecting inbound get scored at -433 to -523 (threshold is -100), so Prysm disconnects them:
  DEBUG p2p: Initiate peer disconnection
    agent=Lighthouse/v8.1.1
    error=gossip scorer: gossip score below threshold: got -433.034821 - threshold -100.000000
```
[2026-03-04T17:09:20.882000+00:00] potuz: no that's clearly now that we are out
[2026-03-04T17:09:37.036000+00:00] potuz: but we were never stuck and suddently started disconnecting them all
[2026-03-04T17:09:47.174000+00:00] potuz: because their score when negative
[2026-03-04T17:10:06.571000+00:00] potuz: PRysm is not finding peers, is that normal?
[2026-03-04T17:39:06.697000+00:00] potuz: I'm tracking all my rejection messages to see the cause
[2026-03-04T17:39:17.131000+00:00] potuz: but we did reject a bunch of invalid stuff from everyone
```

</details>
