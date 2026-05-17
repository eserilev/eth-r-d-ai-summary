# Checkpoint sync implementation and testing

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Potuz initiated a discussion about implementing checkpoint sync functionality, asking barnabasbusa what's needed to set up a checkpoint server. After some initial confusion about requirements, potuz clarified that he wants a server to always serve the post-consensus layer state of the finalized checkpoint block root, and requested a checkpoint URL for testing the client side implementation.

Jameshe5018 provided the relevant technical details, identifying `/eth/v2/debug/beacon/states/finalized` as the required endpoint and referencing a PR that needs review (https://github.com/OffchainLabs/prysm/pull/16466), though noting that the core APIs needed for checkpoint sync have already been merged. The discussion concluded with potuz deciding to test locally with SSZ format and observing performance differences between syncing from home versus a pod environment.

The main outcome was confirmation that the necessary beacon APIs for checkpoint sync are available, with potuz proceeding to local testing while noting unexpectedly faster sync performance from his home setup compared to pod-based syncing.

## Participants

- barnabasbusa
- jameshe5018
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T19:03:43.505000+00:00] potuz: <@412614104222531604> I want to work on checkpoint sync next, what do you need to have a checkpoint server working?
[2026-03-05T19:04:00.381000+00:00] barnabasbusa: just checkpoint off of a beacon node
[2026-03-05T19:04:08.349000+00:00] potuz: what is that?
[2026-03-05T19:04:27.705000+00:00] potuz: remember I don't speak beacon API
[2026-03-05T19:04:31.966000+00:00] potuz: is this the state endpoint?
[2026-03-05T19:04:48.589000+00:00] barnabasbusa: You just gotta give it the beacon api url and prysm gonna figure it out
[2026-03-05T19:05:12.383000+00:00] potuz: I mean what state are you going to be serving
[2026-03-05T19:05:28.583000+00:00] barnabasbusa: Yeah that’s a good question
[2026-03-05T19:05:40.975000+00:00] barnabasbusa: Shouldn’t i be asking you that question ?
[2026-03-05T19:06:40.942000+00:00] potuz: Why is it so hard to talk to you? 😄 

Ok, I want this, would you have a checkpoint URL that I can use to test the client side? I can give you a node that does the server side, I want the server to always serve the post CL state of the finalized checkpoint block root
[2026-03-05T19:06:59.281000+00:00] potuz: cc <@1057691141237125190> you've already implemented this exactly right?
[2026-03-05T19:07:32.531000+00:00] jameshe5018: /eth/v2/debug/beacon/states/finalized this is the endpoint. I think that's merged in, but there's this https://github.com/OffchainLabs/prysm/pull/16466 pr that needs to be reviewed
[2026-03-05T19:08:12.807000+00:00] potuz: I don't care about state roots, does checkpoint sync need that?
[2026-03-05T19:08:19.793000+00:00] jameshe5018: https://github.com/offchainlabs/prysm/commit/bf2485eb711c251c789a14781435a09861782e2a this one merged already
[2026-03-05T19:10:04.815000+00:00] jameshe5018: yeah maybe not, ok nvm me then it's already merged in
[2026-03-05T19:10:05.316000+00:00] potuz: what that this mean?
[2026-03-05T19:10:42.104000+00:00] potuz: Ahh nvm, I'll just test with a local ssz
[2026-03-05T19:11:26.287000+00:00] jameshe5018: we have the apis we need to checkpoint sync
[2026-03-05T19:16:11.468000+00:00] potuz: I wonder why is it so much faster to sync from home than the pod
[2026-03-05T19:16:22.712000+00:00] potuz: I'm up to 7200 very quickly
```

</details>
