# Prysm sync implementation and devnet deployment

**Channel:** epbs | **Date:** 2026-03-04

## Summary

Potuz reports that Prysm sync implementation is working and requests the ability to swap images on the devnet if they fall behind. They recommend starting with their tested branch and deploying updates as needed. A key concern is raised about cross-client compatibility - while sync has only been tested against Prysm nodes, they need to verify that other clients serve payloads and blocks correctly to avoid downscoring issues, especially since syncing wasn't originally planned for this devnet.

The devnet has genesis occurring 10 minutes prior to the conversation, with the fork scheduled for epoch 5, giving time for hot patches. Barnabasbusa confirms they can rebuild and roll out image updates quickly once changes are pushed. However, Potuz realizes they haven't tested with mainnet flags (only minimal configuration) and expresses concern about potential bugs related to fork slots being multiples of 160, prompting them to run a quick test with mainnet flags.

**Action items**: Potuz to complete mainnet flags testing and push updates; Barnabasbusa to rebuild and deploy image once ready; verification needed for cross-client block serving compatibility.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T14:38:44.716000+00:00] potuz: We have sync working <@412614104222531604>, if we fall behind on the devnet, will it be possible to swap images?
[2026-03-04T14:39:01.695000+00:00] potuz: I rather you start with this branch that was tested, and if we need to deploy something that catches up, we can do it
[2026-03-04T14:39:32.820000+00:00] potuz: we've only tested syncing against Prysm nodes, we need to check that other clients serve payloads and blocks by root/range correctly
[2026-03-04T14:39:49.825000+00:00] potuz: otherwise we risk downscoring and disconnecting them and we said syncing was not going to be part of this devnet
[2026-03-04T14:41:40.648000+00:00] barnabasbusa: we had genesis 10 mins ago
[2026-03-04T14:41:45.250000+00:00] barnabasbusa: still working on tooling to become available
[2026-03-04T14:41:52.249000+00:00] barnabasbusa: we set gloas for epoch 5
[2026-03-04T14:41:57.833000+00:00] barnabasbusa: so still got some time for hot patches
[2026-03-04T14:42:08.717000+00:00] barnabasbusa: pls let me know when you pushed
[2026-03-04T14:42:14.621000+00:00] barnabasbusa: I'll rebuild the image and roll it out asap
[2026-03-04T14:42:40.047000+00:00] potuz: and you had to change things right? every single kurtosis run I did was epoch 1... now I'm sure you'll find a bug that Prysm can't fork if the fork slot is multiple of 160
[2026-03-04T14:42:53.134000+00:00] potuz: actually I just realized that we never tested this with mainnet flags 🙁
[2026-03-04T14:42:55.583000+00:00] potuz: lol
[2026-03-04T14:43:02.092000+00:00] potuz: we're only testing minimal
[2026-03-04T14:43:54.015000+00:00] potuz: will make a quick run with mainnet flags
[2026-03-04T14:49:58.257000+00:00] potuz: https://tenor.com/view/take-a-sip-tweek-tweak-south-park-s19e6-tweek-x-craig-gif-20604551
[2026-03-04T14:50:02.816000+00:00] potuz: Where is that devnet!
```

</details>
