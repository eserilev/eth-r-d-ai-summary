# Nimbus Prysm connection and payload synchronization issues

---

## 2026-03-19 (epbs)

The discussion centers on connectivity and synchronization issues between Nimbus and Prysm Ethereum clients. 0xshum reports that Nimbus cannot reconnect to Prysm after being disconnected due to orphaned payloads, and while alternative connections to Lodestar/Lighthouse are possible, their byrange responses are inconsistent, leading to further disconnections and downscoring.

The root cause appears to be mutual downscoring between clients due to payload handling issues. Potuz from Prysm explains that serving empty payload by range responses would trigger downscoring, while terencechain reveals that Prysm's own payload by range functionality is broken and returns everything, potentially causing Nimbus to downscore Prysm for bad sequences. A fix is expected to be merged soon.

The discussion concludes with the identification that both clients may be experiencing complementary issues - Nimbus serving problematic payloads and Prysm returning malformed payload sequences - which could explain the persistent connection problems. No immediate resolution is reached, but the technical root causes are clarified with a Prysm fix in progress.

**Participants:** 0xshum, barnabasbusa, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-19T11:40:25.332000+00:00] 0xshum: Would there be a way to remove the restriction for Nimbus from connecting Prysm? 
I think due to some orphaned payloads from Nimbus we were disconnected. Since then we are not able to reconnect, which keeping us out of sync.
[2026-03-19T11:56:29.543000+00:00] barnabasbusa: you should still be able to peer with lodestar/lighthouse
[2026-03-19T12:33:04.872000+00:00] 0xshum: Yes but byrange response are not consistent so we downscored them and disconnected. Will see if there is any workarounds to recover.
[2026-03-19T12:37:33.411000+00:00] 0xshum: But it is still weird that we cannot reconnect with Prysm in the last few days. Would be helpful to double check.
[2026-03-19T18:33:26.450000+00:00] potuz: If this is necessary I can relax the downscoring, but that would be a touchy thing to do
[2026-03-19T18:33:34.275000+00:00] potuz: why are we downscoring you guys?
[2026-03-19T18:34:19.450000+00:00] potuz: I can try and track it. If you serve blocks by range and return empty payload by range that would be a reason to downscore you, if you would just reject replying to payload by range then we shouldn't downscore youi
[2026-03-19T19:45:45.151000+00:00] terencechain: our payload by range is also broken, it returns everything, could it be nimbus downscoring us for returning bad sequence? the PR should be merged soon
[2026-03-19T21:24:15.673000+00:00] 0xshum: Interesting. If that's the case it could be the cause. Thanks for having a look.
```

</details>

---

## 2026-05-09 (epbs)

The discussion centers around connectivity issues between Lighthouse and Prysm clients on the glamsterdam devnet 3, where 0xunclebill reported that Lighthouse couldn't sync properly due to some clients not serving envelopes by range, and Prysm was immediately disconnecting from Lighthouse nodes after receiving status responses. Initially, the team suspected fork digest issues or problems with finalized root validation, with 0xunclebill opening a GitHub issue theorizing about finalized checkpoint mismatches.

However, terencechain identified the actual root cause by examining Prysm logs: the disconnections were due to IP colocation limits being exceeded. Prysm has a hardcoded limit of 5 connections per IP address, but the devnet environment had 21 connections from the same IP (178.105.53.46), triggering the colocation protection mechanism. This caused Prysm to mark the peer as "bad" and send a generic error goodbye message, which appeared as "Fault" disconnections in Lighthouse logs.

The issue is resolved with the understanding that Prysm has a flag to disable the colocation limit for devnet environments, though potuz noted uncertainty about what happens during restarts. The team agreed it would be beneficial to disable this protection mechanism for devnet testing scenarios.

**Participants:** 0xunclebill, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-09T13:04:42.378000+00:00] 0xunclebill: been trying to sync lighthouse today on glamsterdam devnet 3 but I’ve just about given up. It seems some clients (not sure which) just don’t serve envelopes by range
[2026-05-09T13:09:05.947000+00:00] 0xunclebill: Also Prysm is disconnecting from lighthouse nodes almost immediately, I don’t understand why
[2026-05-09T19:43:19.875000+00:00] potuz: Do you have logs both from LH and Prysm? Or timestamps? I can look at Prysm logs and try to figure out why we disconnect from you
[2026-05-09T19:48:29.031000+00:00] 0xunclebill: its as soon as LH sends a status response to prysm, we get disconnected. i can try getting logs when I'm back at my computer

but I opened up an issue with a theory on why this happens here:
https://github.com/OffchainLabs/prysm/issues/16773
[2026-05-09T19:49:04.981000+00:00] potuz: May be an issue with fork digests
[2026-05-09T19:49:17.769000+00:00] potuz: Cc <@833706406699073536> may have an idea here
[2026-05-09T19:49:45.804000+00:00] potuz: Will look into them, but definitely not today
[2026-05-09T19:51:41.398000+00:00] potuz: If it's right after the status response it may really be a fork digest issue
[2026-05-09T19:52:44.516000+00:00] 0xunclebill: I think these are the disconnect conditions, could be wrong though

```
p2ptypes.ErrWrongForkDigestVersion,
p2ptypes.ErrInvalidFinalizedRoot,
p2ptypes.ErrInvalidRequest,
```
[2026-05-09T19:53:42.507000+00:00] 0xunclebill: we were connected to prysm before our node went to shit, so would be surprised if it was fork digest issue
[2026-05-09T19:54:37.072000+00:00] potuz: Your theory on finalization doesn't seem right though. It's true that it may be a thing on the finalized checkpoint you send, but it can't be that it's too old: the snippet that you posted will pass the check of being finalized
[2026-05-09T20:01:45.571000+00:00] 0xunclebill: oh ok yeah i see. i was assuming old finalized roots were getting pruned
[2026-05-09T20:06:01.268000+00:00] 0xunclebill: our fork digests seem to match
[2026-05-09T21:23:59.071000+00:00] potuz: thanks, just got the issue e-mail
[2026-05-09T21:37:55.432000+00:00] terencechain: <@358120958726373381>  <@755590043632140352>  Hello.. the issue I saw in prysm logs isn't a finalized-root mismatch, I verified 0xc8c1… is in the DB. The actual cause is the IP colocation limit:
```
  2026-05-09 09:32:45  DEBUG p2p: Initiate peer disconnection
    agent=Lighthouse/v8.1.3-b53a969  direction=Inbound
    error=peer is from a bad IP: colocation limit exceeded:
          got 21 - limit 5
          for peer 16Uiu2HAmELywMA7nY1Giy7ruYgtky3G9eCZpqVR8S7oM4TAGwrMJ
          with IP 178.105.53.46
```
 So the code path is : `Status.IsBad` → `isfromBadIP` → `exceeds CollocationLimit = 5 (status.go:66, hardcoded)` → `disconnectBadPeer` → `sendGoodByeAndDisconnect(GoodbyeCodeGenericError, …)` That goodbye code maps to "fault/error", which matches the reason: Fault that LH is seeing
[2026-05-09T22:42:21.311000+00:00] 0xunclebill: ah okay thanks!
[2026-05-09T22:44:29.543000+00:00] 0xunclebill: would be nice to disable that for devnets if possible
[2026-05-09T22:48:08.840000+00:00] potuz: We have a flag for this. No clue what will happen if we restart though
```

</details>

