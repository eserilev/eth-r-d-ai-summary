# Devnet-3 client connectivity and sync issues

---

## 2026-05-11 (epbs)

Multiple Ethereum consensus clients are experiencing severe connectivity and sync issues on devnet-3. Prysm, Lighthouse, and Grandine are completely offline, while Lodestar and Teku appear to be the only clients successfully maintaining the network. The core problem stems from clients being unable to sync due to peers not serving "envelopes" (execution payload envelopes) correctly, with users reporting failed attempts to get peers to serve these data structures over the weekend.

The root cause appears to be a consensus bug related to payload validation at block slot 15742, where Prysm encountered an error stating "payload withdrawals do not match expected withdrawals." This created a fork digest mismatch that prevented Prysm from syncing properly. Potuz from the Prysm team identified that Teku nodes are serving what Prysm considers invalid payloads (claiming no payloads exist when there actually are some), causing Prysm to ban these peers for providing inconsistent data. Meanwhile, Lodestar appears to sync successfully, likely because it doesn't perform the same rigorous consistency checks on envelope responses.

The discussion reveals a fundamental disagreement about validation strictness - Prysm prefers to assume malicious intent when receiving invalid data and performs rigorous envelope validation, while other clients appear more permissive. Potuz expressed frustration that his previous request to abandon the envelope serving path entirely (EIP 8237) was dismissed, as this validation complexity is causing these network splits. The immediate solution requires either fixing the payload serving logic across clients or reverting to simpler block-based syncing.

**Participants:** 0xunclebill, barnabasbusa, bharath.vedartham, nflaig, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-11T11:19:50.249000+00:00] barnabasbusa: btw <@363800010518822915> <@755590043632140352> prysm is completely offline on devnet-3
so is lighthouse <@358120958726373381> and grandine <@777935002263617576>
[2026-05-11T11:22:01.990000+00:00] 0xunclebill: yeah i couldnt sync, i tried all weekend but couldnt get peers to serve me envelopes :/
[2026-05-11T11:22:27.643000+00:00] barnabasbusa: have you checked with your local post office?
[2026-05-11T11:22:48.310000+00:00] 0xunclebill: checking
[2026-05-11T11:23:09.522000+00:00] barnabasbusa: lodestar/teku seems to be carrying the network
[2026-05-11T11:23:18.489000+00:00] barnabasbusa: <@586161934425128960> are you guys serving envlopes ?
[2026-05-11T11:23:39.186000+00:00] 0xunclebill: i think i had trouble peering with lodestar, i can try again
[2026-05-11T11:34:49.600000+00:00] potuz: can you tell me by any chance which peer is 16Uiu2HAkzyQAaXFrnNt5rK61EcFSt2trHDuERQWYC6AXRQqeYbCU? I tried accessing 
https://dora.epbs-devnet-3.ethpandaops.io/

But it seems to be offline
[2026-05-11T11:34:51.818000+00:00] potuz: ahh stupid me
[2026-05-11T11:34:55.342000+00:00] potuz: it's not that name 🙂
[2026-05-11T11:35:43.522000+00:00] potuz: ahhh nevermind, it's caplin sending any fork digest
[2026-05-11T11:41:08.079000+00:00] potuz: Prysm is on an old finalized checkpoint, it seems we did not like a finalization <@412614104222531604>
[2026-05-11T11:41:11.389000+00:00] potuz: analyzing
[2026-05-11T11:46:34.032000+00:00] potuz: <@358120958726373381> I am seeing some errors on PRysm trying to serve you but it's hard to find out cause we're broken anyway
```
[2026-05-11 11:06:37.86] DEBUG p2p: Peer disconnected agent=Lighthouse/v8.1.3-27cfa18/x86_64-linux direction=Outbound multiAddr=/ip4/178.105.51.90/udp/9001/quic-v1/p2p/16Uiu2HAkyYzoKommYCsEofJk7PzssLtTFQ7G36UZu7ZsbYZqQbwk remainingActivePe
ers=1
[2026-05-11 11:06:37.98] DEBUG sync: Could not send execution payload envelope chunk error=Application error 0x0 (local)
[2026-05-11 11:06:37.98] DEBUG sync: Could not write to stream error=Application error 0x0 (local)
[2026-05-11 11:06:37.98] DEBUG sync: Could not handle p2p RPC error=Application error 0x0 (local) peer=16Uiu2HAkyYzoKommYCsEofJk7PzssLtTFQ7G36UZu7ZsbYZqQbwk topic=/eth2/beacon_chain/req/execution_payload_envelopes_by_range/1/ssz_snappy
```
[2026-05-11T11:47:48.724000+00:00] nflaig: hahah I sometimes have caplins in my dht when doing local kurtosis runs <@687974325072035882> it's probably worth fixing this, I remember this happened all the way back in holesky already and was a pain to debug at times
[2026-05-11T11:50:28.516000+00:00] potuz: I see a fork digest mistmatch on Prysm, I honestly don't even understand how we managed to sync for a couple of days 😅
[2026-05-11T11:52:59.372000+00:00] potuz: I think we're failing to sync cause Lodestar/Teky is not serving payload by range correctly <@586161934425128960> <@504202741933932544>, so we are now in the same boat as LH. We can't sync past 23443 cause we're missing it. I haven't found the reason why we fell out of sync, but that's the reason we can't catch up
[2026-05-11T11:55:23.855000+00:00] nflaig: I have a node synced to 34857, we have started a few full nodes in our dev infra to support the network more
[2026-05-11T11:56:45.835000+00:00] nflaig: we should, I had done some analysis last week on some nodes and seems like lodestar peers are the only ones serving us data
[2026-05-11T11:56:49.443000+00:00] 0xunclebill: i cant get lodestar peers. when i try to add manually by enr lodestar is either unreachable or the libp2p handshake times out*
[2026-05-11T11:56:55.867000+00:00] potuz: This is the real reason  it seems https://dora.glamsterdam-devnet-3.ethpandaops.io/slot/15742

This block is finalized and Prysm did not like it at all, it seems to be a consensus bug
```
[2026-05-08 20:10:48.20] ERROR sync: Could not handle p2p pubsub error=payload withdrawals do not match expected withdrawals topic=/eth2/5cf894ad/execution_payload/ssz_snappy
```
[2026-05-11T11:57:23.274000+00:00] potuz: cc <@363800010518822915> haven't checked if we're right or not, can I get access to a lodestar node <@412614104222531604> to get the block?
[2026-05-11T11:57:26.480000+00:00] nflaig: but payload is orphaned?
[2026-05-11T11:58:00.509000+00:00] barnabasbusa: yeah i also keep seeing this
[2026-05-11T11:58:16.376000+00:00] potuz: yeah Dora is saying so
[2026-05-11T11:58:16.789000+00:00] barnabasbusa: seems like some caplin node subscribes to every network
[2026-05-11T11:58:38.033000+00:00] potuz: but I suspect we start breaking completely from Teku after they start sending us invalid payloads
[2026-05-11T11:59:10.502000+00:00] barnabasbusa: One man’s invalid is another man’s valid 🤣
[2026-05-11T11:59:18.330000+00:00] nflaig: I had a last from friday here https://gist.github.com/nflaig/5647e048594d21d2a24970d67dfc791a, but seems teku/nimbus, Enrico already found the issue
[2026-05-11T11:59:37.455000+00:00] barnabasbusa: you can directly curl the bn api endpoint
[2026-05-11T12:00:51.204000+00:00] 0xunclebill: those invalid payloads was also what broke us, they've been sending them for awhile though and prysm seemed fine
[2026-05-11T12:00:59.121000+00:00] 0xunclebill: until now
[2026-05-11T12:01:31.513000+00:00] potuz: Yeah then I see a bunch of Prysm downscoring peers for sending bad by range requests
```
[2026-05-09 20:59:11.36] DEBUG initial-sync: Failed to fetch payloads error=envelope does not match block: invalid data returned from peer
```
I really wish you guys hadn't dismissed my request to drop this path altogether
[2026-05-11T12:01:43.896000+00:00] potuz: we should have merged EIP 8237
[2026-05-11T12:02:25.317000+00:00] potuz: We complain that someone is sending blocks and claiming there were no payloads
[2026-05-11T12:02:29.026000+00:00] potuz: when there were some
[2026-05-11T12:03:03.826000+00:00] potuz: cc <@504202741933932544> `16Uiu2HAmUALppyi9RBwftvZjGqtkq3iYEd923BL4Ta1LSts1Tbx`is Teku-geth-2
[2026-05-11T12:03:11.837000+00:00] potuz: we ban you for lying on the payloads
[2026-05-11T12:03:29.050000+00:00] potuz: ```
[2026-05-09 20:59:11.23] DEBUG initial-sync: Failed to fetch payloads error=block with root 0xc648ffc5ee12b9aa51b6b818d4f8254d975d04d69d7fab4b1498f38f870b388f does not build on top of the empty block: invalid data returned from peer
```
[2026-05-11T12:03:34.680000+00:00] barnabasbusa: how did lodestar sync to this then? No validation of the envelope?
[2026-05-11T12:03:43.514000+00:00] potuz: Most probably they don't care
[2026-05-11T12:03:47.947000+00:00] barnabasbusa: also why wouldn't prysm just fork it ?
[2026-05-11T12:03:51.146000+00:00] potuz: and don't check for consistency of the reply
[2026-05-11T12:03:58.500000+00:00] potuz: I rather be rigurous and catch these bugs early
[2026-05-11T12:04:20.291000+00:00] potuz: you can sync assuming your peers are honest and possibly buggy
[2026-05-11T12:04:25.256000+00:00] barnabasbusa: I'd expect lighthouse/prysm to have formed a fork where they would agree with each other
[2026-05-11T12:04:28.280000+00:00] potuz: I rather assume anyone sending me invalid data is malicious
[2026-05-11T12:04:45.302000+00:00] potuz: we can't cause we have a large majority of the chain telling us the chain is advancing
[2026-05-11T12:04:49.951000+00:00] potuz: and advertising higher head
[2026-05-11T12:04:58.311000+00:00] potuz: but then we request and we can't catch up
[2026-05-11T12:05:04.746000+00:00] potuz: both Prysm and LH are trying to do the same it seems
[2026-05-11T12:05:33.887000+00:00] barnabasbusa: so the only solution would be to slash all teku validators and fork abck to the prysm chain?
[2026-05-11T12:05:34.442000+00:00] potuz: we need to fix the serving of payloads, or --yet again-- ditch it entirely and just sync with blocks
[2026-05-11T12:06:28.718000+00:00] barnabasbusa: hm looks like buildoor's prysm is actually at the same head slot as tekus
[2026-05-11T12:06:30.792000+00:00] potuz: I did my analyzing for today, will wait till <@363800010518822915> may check if I'm right or not, moving to review PRs to not be fired
[2026-05-11T12:06:54.590000+00:00] barnabasbusa: buildoor's prysm is using a fork of your <@1386598056320831509>'s fork
[2026-05-11T12:07:01.794000+00:00] barnabasbusa: 
[2026-05-11T12:07:20.829000+00:00] barnabasbusa: <@1386598056320831509> did you disable some envelope checking logic in your fork?
[2026-05-11T12:11:09.474000+00:00] potuz: Nah, probably that node was never restarted or never feel out of sync
[2026-05-11T12:11:25.299000+00:00] potuz: Why in the hell Discord replied to tomo instead of you
[2026-05-11T12:11:34.965000+00:00] bharath.vedartham: hmm i m using the same prysm fork with just the proposer preferences event and payload attributes event
[2026-05-11T12:11:45.644000+00:00] bharath.vedartham: i didn't explicitly disable any envelope checking logic
[2026-05-11T12:12:06.958000+00:00] potuz: That node probably never needed to request a range
[2026-05-11T12:13:31.544000+00:00] barnabasbusa: ah yeah thats possible if there was no new commit watchtower wouldn't pick it up
```

</details>

