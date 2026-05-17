# Docker build configuration and tag management

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around a Docker build configuration issue where terencechain initially used a full commit hash instead of a branch name when building a Prysm beacon chain image. barnabasbusa explained that while SHA256 commit hashes work for the build process, they create Docker images with unwieldy tags like "OffchainLabs-0c8b0141111848f4747503579842de9efe648c68-0c8b014". The correct approach is to reference the branch name directly and avoid using docker tag override to get cleaner, more manageable tags.

After the clarification, terencechain corrected the build command to `/build client-cl client:Prysm ref:epbs-devnet-0-sync` and triggered a new build job. Meanwhile, potuz expressed urgency about deploying the build due to time constraints, offering to run binaries directly on the host or run validators from home, but discovered his available binary was compiled for ARM architecture rather than the required platform.

The immediate action items include waiting for the corrected Docker build to complete, with terencechain stepping away for an hour and the build process running in the background.

## Participants

- barnabasbusa
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T01:17:49.963000+00:00] terencechain: https://github.com/ethpandaops/eth-client-docker-image-builder/actions/runs/22696598066/job/65805868938 this was the job, did i fuck up
[2026-03-05T01:18:56.344000+00:00] barnabasbusa: how did you build jt ?
[2026-03-05T01:19:10.065000+00:00] barnabasbusa: did you do docker tag override ?
[2026-03-05T01:19:23.030000+00:00] barnabasbusa: you gotta reference the branch name
[2026-03-05T01:19:33.269000+00:00] barnabasbusa: if you want it to use that as a tag
[2026-03-05T01:20:02.592000+00:00] barnabasbusa: looks like you pasted the whole commit
[2026-03-05T01:20:12.357000+00:00] barnabasbusa: yes lol
[2026-03-05T01:20:15.124000+00:00] terencechain: yes i ref the commit
[2026-03-05T01:20:24.158000+00:00] barnabasbusa: ref is branch name ser
[2026-03-05T01:20:37.748000+00:00] barnabasbusa: then it will update the old branch’s name
[2026-03-05T01:20:40.505000+00:00] terencechain: oh i read it and it says sha256 works too
[2026-03-05T01:20:54.172000+00:00] barnabasbusa: it does but it will also then build an image with that tag
[2026-03-05T01:20:56.003000+00:00] terencechain: so i assume thats the commit hash
[2026-03-05T01:21:07.871000+00:00] terencechain: gotcha
[2026-03-05T01:21:09.187000+00:00] barnabasbusa: https://hub.docker.com/layers/ethpandaops/prysm-beacon-chain/OffchainLabs-0c8b0141111848f4747503579842de9efe648c68-0c8b014/images/sha256-715c74c8ea6edc86a7c474b0028f8c76c9e0e2fab8da49760ab8326be87ae6e1
[2026-03-05T01:21:17.661000+00:00] barnabasbusa: like this
[2026-03-05T01:21:24.209000+00:00] potuz: dude why do you speak this language that no one understands
[2026-03-05T01:21:27.876000+00:00] terencechain: thank you ser
[2026-03-05T01:21:40.180000+00:00] barnabasbusa: Terence gets it
[2026-03-05T01:21:45.212000+00:00] potuz: what is going on, do we need to wait another 30 minutes?
[2026-03-05T01:21:46.127000+00:00] terencechain: i obv did not lol
[2026-03-05T01:21:49.275000+00:00] barnabasbusa: don’t do docker tag override
[2026-03-05T01:21:50.726000+00:00] potuz: I need to sleep
[2026-03-05T01:22:02.116000+00:00] terencechain: i need to goto costco now and get some ice cream, ill be back in an hour
[2026-03-05T01:22:07.865000+00:00] barnabasbusa: lmao
[2026-03-05T01:22:25.199000+00:00] barnabasbusa: did you trigger a new build ?
[2026-03-05T01:22:35.914000+00:00] potuz: nah man deploy the damned thing , can I have access to metal I'll upload the actual binary and will run in there
[2026-03-05T01:22:39.158000+00:00] potuz: or give me the keuys
[2026-03-05T01:22:44.031000+00:00] potuz: I'll run the vals at home
[2026-03-05T01:22:51.452000+00:00] barnabasbusa: keys are on the host
[2026-03-05T01:23:00.582000+00:00] barnabasbusa: you can just stop the beacon node and run the binary
[2026-03-05T01:23:09.877000+00:00] terencechain: oh i thought <@412614104222531604> triggered it
[2026-03-05T01:23:12.860000+00:00] terencechain: ok let me do it again
[2026-03-05T01:23:13.612000+00:00] barnabasbusa: just run on port 5052
[2026-03-05T01:23:25.309000+00:00] barnabasbusa: nah man I’m already half asleep in bed
[2026-03-05T01:23:45.273000+00:00] terencechain: <@412614104222531604> is this good?

/build client-cl client:Prysm ref:epbs-devnet-0-sync
[2026-03-05T01:23:50.747000+00:00] terencechain: or i need to add more fields?
[2026-03-05T01:23:53.220000+00:00] barnabasbusa: Yes
[2026-03-05T01:23:58.547000+00:00] barnabasbusa: that’s all
[2026-03-05T01:24:18.653000+00:00] terencechain: https://github.com/ethpandaops/eth-client-docker-image-builder/actions/runs/22697783737
[2026-03-05T01:24:32.337000+00:00] potuz: ahh crap the binary I have is arm...
[2026-03-05T01:24:36.852000+00:00] potuz: life doesn't like me
```

</details>
