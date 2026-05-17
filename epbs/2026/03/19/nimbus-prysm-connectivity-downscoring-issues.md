# Nimbus-Prysm connectivity and downscoring issues

**Channel:** epbs | **Date:** 2026-03-19

## Summary

Nimbus is experiencing connectivity issues with Prysm nodes, unable to reconnect after being disconnected due to orphaned payloads. While Nimbus can still peer with Lodestar and Lighthouse, they are getting inconsistent byrange responses that trigger downscoring and disconnections, preventing proper sync.

The root cause appears to be mutual downscoring between the clients. Prysm downgrades peers that serve blocks by range but return empty payload by range responses. However, terencechain revealed that Prysm's own payload by range implementation is broken and returns everything, which could be causing Nimbus to downscore Prysm nodes for returning bad sequences.

Potuz offered to relax downscoring if necessary but noted it would be a sensitive change. The discussion concludes with the identification that Prysm's broken payload by range feature is likely the mutual cause, with a fix in an upcoming PR that should resolve the connectivity issues between the two clients.

## Participants

- 0xshum
- barnabasbusa
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

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
