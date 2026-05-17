# Branch Building and Deployment Issues

**Channel:** epbs | **Date:** 2026-03-04

## Summary

The discussion centers around build and deployment issues with the `epbs-devnet-0-sync` branch. Initially, terencechain encountered compilation errors related to undefined methods `FullHead` and `HasPayload` in the ForkChoicer interface and BlockAndCheckpoints type respectively. However, potuz confirmed the branch was building cleanly on his system at commit `c32441537f1d2f745f275fbf605709aef1aa5827`.

The build issue was eventually resolved when terencechain performed a `bazel clean`, suggesting it was a local cache problem rather than actual code issues. Meanwhile, potuz identified that peer downscoring was occurring due to rate-limiting and mentioned wanting to investigate whether it was their fault, though he indicated he could fix the underlying request issue in the branch.

The team successfully deployed the updated branch to the devnet, with parithosh building the image and redeploying it to `prysm-geth-1`. Potuz mentioned wanting to examine logs from pods to understand the cause of the downscoring issue, indicating this remains an ongoing investigation point for network performance optimization.

## Participants

- barnabasbusa
- parithosh
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T16:19:33.087000+00:00] potuz: the branch that can sync is `epbs-devnet-0-sync`
[2026-03-04T16:20:07.847000+00:00] barnabasbusa: building
[2026-03-04T16:21:32.444000+00:00] terencechain: the branch cant build
[2026-03-04T16:21:37.385000+00:00] terencechain: i already pinged potuz on the side
[2026-03-04T16:21:42.788000+00:00] terencechain: can you check?
[2026-03-04T16:21:54.422000+00:00] potuz: Oh what happened? I was running it before this devnet 🙁
[2026-03-04T16:22:10.008000+00:00] terencechain: ```
beacon-chain/blockchain/receive_attestation.go:137:68: s.cfg.ForkChoiceStore.FullHead undefined (type "github.com/OffchainLabs/prysm/v7/beacon-chain/forkchoice".ForkChoicer has no field or method FullHead)
beacon-chain/blockchain/setup_forkchoice.go:130:7: bcp.HasPayload undefined (type *"github.com/OffchainLabs/prysm/v7/beacon-chain/forkchoice/types".BlockAndCheckpoints has no field or method HasPayload)
compilepkg: error running subcommand external/go_sdk/pkg/tool/darwin_arm64/compile: exit status 2
ERROR: /Users/t/prysm/beacon-chain/blockchain/BUILD.bazel:3:11: Running nogo on //beacon-chain/blockchain:go_default_library failed: (Exit 1): builder failed: error executing RunNogo command (from target //beacon-chain/blockchain:go_default_library) bazel-out/darwin_arm64-opt-exec-ST-d57f47055a04/bin/external/go_sdk/builder_reset/builder nogo -sdk external/go_sdk -goroot bazel-out/darwin_arm64-fastbuild/bin/external/io_bazel_rules_go/stdlib_ ... (remaining 342 arguments skipped)

Use --sandbox_debug to see verbose messages from the sandbox and retain the sandbox build root for debugging
nogo: nogo: error running analyzers: 163 analyzers skipped due to type-checking error: beacon-chain/blockchain/receive_attestation.go:137:68: s.cfg.ForkChoiceStore.FullHead undefined (type "github.com/OffchainLabs/prysm/v7/beacon-chain/forkchoice".ForkChoicer has no field or method FullHead)
INFO: Found 1 target...
```
[2026-03-04T16:22:11.851000+00:00] potuz: give me a while barnabas, will also look into the downscoring, want to know if it's our fault or not
[2026-03-04T16:23:31.523000+00:00] barnabasbusa: https://klipy.com/gifs/gold-mic-hand-off-2
[2026-03-04T16:23:33.970000+00:00] barnabasbusa: <@199561711278227457>
[2026-03-04T16:23:59.784000+00:00] parithosh: https://giphy.com/gifs/oceanpark-roy-chiken-ocean-park-XDtaeHoNWcgjpVa4Aq
[2026-03-04T16:25:20.916000+00:00] potuz: will add a couple of commits to that sync branch and we'll try it Pari, but I want to download first logs from these pods to try to see what caused the downscore
[2026-03-04T16:25:48.076000+00:00] parithosh: sure, lemme know once you're done commiting stuff and ill build + deploy after
[2026-03-04T16:35:21.286000+00:00] potuz: <@363800010518822915> it builds cleanly here!
[2026-03-04T16:35:28.115000+00:00] potuz: did you pull?
[2026-03-04T16:35:46.739000+00:00] terencechain: im on commit: `c32441537f1d2f745f275fbf605709aef1aa5827`
[2026-03-04T16:36:28.395000+00:00] potuz: yep
```$ bazel build //cmd/beacon-chain
WARNING: Build option --platforms has changed, discarding analysis cache (this can be expensive, see https://bazel.build/advanced/performance/iteration-speed).
INFO: From BazelWorkspaceStatusAction stable-status.txt:
date: illegal option -- -
usage: date [-jnRu] [-I[date|hours|minutes|seconds|ns]] [-f input_fmt]
            [ -z output_zone ] [-r filename|seconds] [-v[+|-]val[y|m|w|d|H|M|S]]
            [[[[mm]dd]HH]MM[[cc]yy][.SS] | new_date] [+output_fmt]
date: illegal option -- -
usage: date [-jnRu] [-I[date|hours|minutes|seconds|ns]] [-f input_fmt]
            [ -z output_zone ] [-r filename|seconds] [-v[+|-]val[y|m|w|d|H|M|S]]
            [[[[mm]dd]HH]MM[[cc]yy][.SS] | new_date] [+output_fmt]
INFO: Analyzed target //cmd/beacon-chain:beacon-chain (1392 packages loaded, 18049 targets configured).
INFO: From GoLink cmd/beacon-chain/beacon-chain_/beacon-chain:
ld: warning: ignoring duplicate libraries: '-lc++', '-lm', 'bazel-out/darwin_arm64-fastbuild/bin/external/com_github_supranational_blst/libasm.a', 'bazel-out/darwin_arm64-fastbuild/bin/external/com_github_supranational_blst/libsrc.a'
INFO: Found 1 target...
Target //cmd/beacon-chain:beacon-chain up-to-date:
  bazel-bin/cmd/beacon-chain/beacon-chain_/beacon-chain
INFO: Elapsed time: 50.507s, Critical Path: 30.62s
INFO: 2031 processes: 1 internal, 2030 darwin-sandbox.
INFO: Build completed successfully, 2031 total actions````
[2026-03-04T16:36:41.739000+00:00] potuz: clean the bazel cache?
[2026-03-04T16:37:41.374000+00:00] terencechain: still doesnt work, you are on commit: `c32441537f1d2f745f275fbf605709aef1aa5827` and there's no other unstaged changes?
[2026-03-04T16:38:04.978000+00:00] terencechain: https://github.com/OffchainLabs/prysm/commit/c32441537f1d2f745f275fbf605709aef1aa5827
[2026-03-04T16:38:30.790000+00:00] potuz: $ git log -1
commit c32441537f1d2f745f275fbf605709aef1aa5827 (HEAD -> epbs-devnet-0-sync, origin/epbs-devnet-0-sync)
Author: Potuz <potuz@prysmaticlabs.com>
Date:   Wed Mar 4 11:36:24 2026 -0300

    Load states by hash when root fails
potuz<@755590043632140352>-C-CF7F2H~/Documents/code/ethereum/prysm: 13:38:02 ±[●][epbs-devnet-0-sync] $ git status
On branch epbs-devnet-0-sync
Your branch is up to date with 'origin/epbs-devnet-0-sync'.
[2026-03-04T16:38:46.211000+00:00] potuz: builds cleanly
[2026-03-04T16:39:08.536000+00:00] terencechain: hm ok <@199561711278227457> <@412614104222531604> you guys can try `epbs-devnet-0-sync` then
[2026-03-04T16:39:13.727000+00:00] potuz: <@199561711278227457> can you deploy that branch
[2026-03-04T16:39:17.128000+00:00] potuz: jinx
[2026-03-04T16:39:40.805000+00:00] potuz: nuke one node and deploy it, will still need to check why the hell we're downscoring peers, seems to be rate-limiting
[2026-03-04T16:45:03.582000+00:00] potuz: Ahh I know why we are requesting them. I can fix that in that branch just in case
[2026-03-04T16:47:22.158000+00:00] parithosh: are there more commits coming or can i bulid and deploy?
[2026-03-04T16:47:44.501000+00:00] potuz: Give me 1 minute I want to validate something will ping you in a couple of minutes
[2026-03-04T17:02:26.151000+00:00] parithosh: `epbs-devnet-0-sync` commit `c324415`?
[2026-03-04T17:03:42.339000+00:00] potuz: yeah
[2026-03-04T17:03:51.362000+00:00] potuz: if it builds for you, for some reason Terence can't build it
[2026-03-04T17:04:10.597000+00:00] terencechain: sorry for the noise.. i can  build now, i had to bazel clean 🙁
[2026-03-04T17:04:18.877000+00:00] parithosh: it built for me
[2026-03-04T17:04:32.177000+00:00] parithosh: cleaning prysm-geth-1  and re-deploying with your new image
[2026-03-04T17:04:52.884000+00:00] potuz: thanks
[2026-03-04T17:08:27.560000+00:00] parithosh: redeployed
```

</details>
