# Lighthouse sync issues on glamsterdam devnet

**Channel:** epbs | **Date:** 2026-05-09

## Summary

0xunclebill reported sync issues with Lighthouse on the glamsterdam devnet, experiencing problems with clients not serving envelopes by range and Prysm nodes disconnecting from Lighthouse immediately after status responses. Initially suspecting fork digest mismatches or finalized root issues, 0xunclebill opened a GitHub issue theorizing about finalization-related disconnections.

After investigation, terencechain identified the actual root cause from Prysm logs: an IP colocation limit being exceeded (21 peers from the same IP when the hardcoded limit is 5). This triggers Prysm's bad peer detection, causing it to send a "Fault" goodbye message and disconnect. The issue appears specific to devnet environments where multiple nodes may share IP addresses.

The team confirmed that Prysm has a flag to disable this colocation limit, though potuz noted uncertainty about the effects of restarting with this configuration. The consensus was that disabling this limit for devnets would be beneficial to avoid these false positive disconnections.

## Participants

- 0xunclebill
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
