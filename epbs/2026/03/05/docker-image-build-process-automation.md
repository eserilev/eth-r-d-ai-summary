# Docker image build process and automation

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around testing and deployment of Prysm client updates through an automated Docker image build process. Terence reported successful testing results, confirming that Prysm can sync to head 10 out of 10 times, though minor bugs remain to be resolved. The team identified ongoing issues with local reorganizations caused by validators voting with committee index 0 in attestations, which is providing unintended stress testing for their forkchoice implementation.

The deployment process uses a watchtower system that checks for new Docker images every 15 minutes and automatically updates running containers. Potuz was eager to see the new beacon node deployed to achieve chain finalization, and there was discussion about manually restarting pods versus waiting for the automated update cycle. The team also noted that a notification channel has become too noisy with hive test runs and considered disabling those automated messages.

Terence confirmed he kept the fix clean without adding temporary hacks to avoid accidentally merging test code into the development branch. The overall sentiment was positive about the recovery progress, with the client showing improved stability despite the ongoing reorg issues during sync.

## Participants

- barnabasbusa
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T00:41:59.337000+00:00] terencechain: https://github.com/ethpandaops/eth-client-docker-image-builder/actions/runs/22696598066
[2026-03-05T00:42:14.755000+00:00] potuz: holy fuck!
[2026-03-05T00:45:58.003000+00:00] barnabasbusa: it’s been pinned for almost a year lol
[2026-03-05T00:46:10.626000+00:00] barnabasbusa: welcome to the 21st century
[2026-03-05T00:46:13.352000+00:00] potuz: that channel is spam
[2026-03-05T00:46:28.727000+00:00] barnabasbusa: it really has become one :/
[2026-03-05T00:46:35.737000+00:00] terencechain: ok ive verified we can sync to head 10/10 times
[2026-03-05T00:46:50.988000+00:00] terencechain: prysm still have some minor bugs to resolve but overall looking good to recover
[2026-03-05T00:46:55.155000+00:00] barnabasbusa: I think we might wanna disable hive runs there as it’s too noisy
[2026-03-05T00:47:13.237000+00:00] terencechain: there's still many local reorg logs because i think ppl are voting 0 in atts committee index, we should fix that
[2026-03-05T00:47:39.829000+00:00] potuz: yeah we're reorging constantly while syncing
[2026-03-05T00:48:12.191000+00:00] terencechain: its awesome we are getting nice stress reorg test for our forkchoice implementation though
[2026-03-05T00:54:02.305000+00:00] potuz: how long until I can see the new beacon node
[2026-03-05T01:01:11.856000+00:00] barnabasbusa: We have a watchtower running
[2026-03-05T01:01:31.355000+00:00] barnabasbusa: that container runs a check every 15 mins seeing if there is a new image available
[2026-03-05T01:01:50.509000+00:00] barnabasbusa: can check the watchtower logs for when the next check is happening if you are inpatient
[2026-03-05T01:02:25.463000+00:00] potuz: yeah I want to go to bed with the chain finalizing
[2026-03-05T01:02:44.736000+00:00] potuz: the build is already finished
[2026-03-05T01:02:52.806000+00:00] potuz: can I wipe the pod and restart it?
[2026-03-05T01:03:01.739000+00:00] potuz: or how do I check if it will pick up the new image?
[2026-03-05T01:03:37.360000+00:00] potuz: and how do I check that Terence sent the right commit 🙂
[2026-03-05T01:04:52.334000+00:00] barnabasbusa: look at the logs of watchtower
[2026-03-05T01:06:32.720000+00:00] potuz: I don't know what that is I restarted just in case
[2026-03-05T01:06:44.972000+00:00] potuz: <@363800010518822915> did you also add the /20 there? is syncing fast!
[2026-03-05T01:06:57.370000+00:00] potuz: ahh no
[2026-03-05T01:07:31.269000+00:00] terencechain: naa i didnt add any hack, want to keep it clean, so we dont merge it to develope by mistake
```

</details>
