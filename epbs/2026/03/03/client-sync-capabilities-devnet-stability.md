# Testing client interoperability and chain branching issues

**Channel:** epbs | **Date:** 2026-03-03

## Summary

The discussion centers on testing client interoperability between Ethereum consensus clients (Prysm, Lighthouse, Lodestar, Nimbus, and Teku), focusing on chain branching behavior and reorg scenarios. Potuz confirmed a fix that eliminated reorgs and achieved 100% attestation inclusion, with Prysm missing every slot multiple of 5 as intended. However, testing revealed concerning interoperability issues: with the latest images, Prysm and Lodestar nodes followed one chain while Lighthouse nodes failed to maintain consensus even with each other, creating separate chains.

Further testing showed mixed results across different configurations. While some participants (0xunclebill and barnabasbusa) reported successful interoperability between clients, Potuz consistently observed Lighthouse nodes forking from each other even in simple two-client setups. The team identified potential contributing factors including payload timing (payloads arriving before blocks), the use of `--image-download-always` flag, and hardware differences that might affect payload processing timing.

The discussion concludes with ongoing investigation into the root cause of Lighthouse's self-forking behavior, with participants planning to test payload timing scenarios and debug configuration differences. Barnabasbusa noted plans to expand testing to include all five major consensus clients, while the team continues to isolate whether the issues stem from payload delivery timing or other client-specific factors.

## Participants

- 0xunclebill
- barnabasbusa
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-03T00:05:18.164000+00:00] potuz: confirmed the fix <@586161934425128960> no reorgs, 100% attestation inclusion and every slot multiple of 5 is missed by Prysm
[2026-03-03T00:05:56.034000+00:00] potuz: Will move to actual malicious reorgs instead of simple misses, although those do not necessarily need to be tested at this stage
[2026-03-03T00:07:07.690000+00:00] nflaig: thanks for the testing 🙏
[2026-03-03T00:18:44.587000+00:00] potuz: not sure if this was updated in pandaops, but with the latest images, Prysm and Lodestar follow one chain, and Lighthouse doesn't follow even Lighthouse. There Prysm + Lodestar (4) nodes on one chain and the other two chains are the two LH nodes.
[2026-03-03T00:19:01.806000+00:00] potuz: no shenanigans with missed nodes or payloads in that branch
[2026-03-03T00:31:42.636000+00:00] 0xunclebill: i think a new build just got triggered like 15min ago. I'm able to follow along with lodestar without any issues. not testing any shenangians yet though
[2026-03-03T00:32:09.974000+00:00] potuz: but Lodestar also adds a small delay to sending the payload
[2026-03-03T00:32:28.750000+00:00] 0xunclebill: ok let me try with prysm real quick
[2026-03-03T00:33:54.500000+00:00] potuz: launching a LH+Prysm alone on the latest pandaops branch
[2026-03-03T00:35:59.789000+00:00] 0xunclebill: same
[2026-03-03T00:38:19.181000+00:00] potuz: Prysm--LH alone is not branching at all, up to epoch 5 now
[2026-03-03T00:39:54.530000+00:00] potuz: Got a fork after a few epochs, LH vs LH again, if you want I can send you logs, signing off though, need to force myself out of the keyboard
[2026-03-03T00:48:02.416000+00:00] 0xunclebill: feel free to send them, but im sure ill see the same logs in a few epoch. thanks!
[2026-03-03T03:02:42.270000+00:00] 0xunclebill: were you running the devnets with `--image-download-always` by chance? im seeing things run okay between LH and prysm, just trying to figure out what im missing here
[2026-03-03T07:43:54.312000+00:00] barnabasbusa: I was able to also get prysm/lh/lodestar interop on kt
[2026-03-03T07:44:10.706000+00:00] barnabasbusa: gonna add nimbus and teku into the mix as well
[2026-03-03T08:21:07.318000+00:00] barnabasbusa: prysm bugs:
[2026-03-03T09:23:17.419000+00:00] potuz: Yeah I'm running with it. I'm running in powerful metal and that can contribute to get more payloads before blocks
[2026-03-03T09:29:49.834000+00:00] 0xunclebill: hmm okay I’ll try purposely publishing payloads before blocks and debug, prolly just something stupid I missed on my end
[2026-03-03T09:35:26.610000+00:00] potuz: I don't think the issue was with Payloads before blocks cause LH reorgs itself, so if you're delaying Payloads then that shouldn't be the case. But it could be that one LH node got a Payload that the other couldn't
```

</details>
