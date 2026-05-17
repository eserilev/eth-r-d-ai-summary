# Lighthouse Prysm devnet connectivity and disconnection issues

---

## 2026-05-09 (epbs)

The discussion centers around connectivity issues between Lighthouse and Prysm clients on the glamsterdam devnet 3, where 0xunclebill reported that Lighthouse was having difficulty syncing due to some clients not serving envelopes by range, and Prysm nodes were disconnecting from Lighthouse immediately after receiving status responses. Initially, the team suspected this might be related to fork digest mismatches or finalized root issues, with 0xunclebill opening a GitHub issue theorizing about finalization-related disconnect conditions.

However, terencechain identified the actual root cause by examining Prysm logs: the disconnections were occurring due to IP colocation limits being exceeded (21 connections from the same IP when the hardcoded limit is 5), not finalization issues. The disconnect sequence follows: `Status.IsBad` → `isfromBadIP` → `exceeds CollocationLimit` → `disconnectBadPeer` → `sendGoodByeAndDisconnect(GoodbyeCodeGenericError)`, which appears as a "Fault" reason in Lighthouse logs.

The team noted that Prysm has a flag to disable the colocation limit for devnets, though potuz mentioned uncertainty about what would happen if they restart the node. This represents a clear resolution to the connectivity issue, with the solution being to adjust the colocation limit configuration for devnet environments.

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

