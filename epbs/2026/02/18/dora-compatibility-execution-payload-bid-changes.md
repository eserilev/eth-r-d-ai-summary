# Dora compatibility with execution payload bid changes

**Channel:** epbs | **Date:** 2026-02-18

## Summary

The discussion centered around compatibility issues between Dora (a block explorer tool) and recent changes to execution payload bids in Ethereum's ePBS (enshrined Proposer-Builder Separation) development. The user 0xunclebill encountered a JSON decoding error related to missing "blob KZG commitments root" when trying to use Dora with the latest execution payload bid changes.

The issue was resolved by specifying `dora_params` in the configuration, as referenced in the ePBS devnet documentation. However, there were additional concerns about using outdated Dora images and potential tooling bugs while waiting for real clients to finalize implementations. The team also noted that Consensoor has p2p peering limitations and can only connect to other Consensoor instances, recommending its removal from interop configurations.

By the end of the discussion, 0xunclebill reported progress with a local Lighthouse-only network that was working and finalizing, though they still needed to resolve some issues before being ready for multi-client interoperability testing. The conversation highlighted ongoing challenges in the ePBS tooling ecosystem as teams work toward stable client implementations.

## Participants

- 0xunclebill
- barnabasbusa
- jtraglia
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-18T19:33:57.250000+00:00] 0xunclebill: does dora work with the latest changes to execution payload bid?

im seeing the following error message
`time="2026-02-18T19:33:05Z" level=warning msg="beacon block stream failed to decode execution_payload_bid event: invalid JSON: blob KZG commitments root missing" client=1-geth-lighthouse service=cl-pool`
[2026-02-18T19:40:43.760000+00:00] terencechain: that looks outdated
[2026-02-18T20:53:44.900000+00:00] jtraglia: It should work 🤔 I know that <@412614104222531604> got Consensoor working in kurtosis 😅
[2026-02-18T20:54:24.429000+00:00] jtraglia: Looks like you need to specify `dora_params`. See this:
https://notes.ethereum.org/@ethpandaops/epbs-devnet-0#Kurtosis-Interop-Conifg-Pre-devnet-testing
[2026-02-18T20:54:58.331000+00:00] 0xunclebill: ah okay thanks!
[2026-02-18T20:56:17.207000+00:00] jtraglia: No problem 🙂 please share if you get it working!
[2026-02-18T20:57:24.372000+00:00] jtraglia: Also, I'm not sure if you can interop with Consensoor. I'd recommend removing that from your config. I believe it has p2p peering issues with real clients.
[2026-02-18T21:30:37.720000+00:00] barnabasbusa: did you fetch the latest dora image?
[2026-02-18T21:30:50.452000+00:00] barnabasbusa: it’s possible we still have some tooling bugs
[2026-02-18T21:31:08.657000+00:00] barnabasbusa: As we are waiting for a real client to ship something to finalized our tooling prs
[2026-02-18T21:31:14.673000+00:00] barnabasbusa: cc <@808969530608451584>
[2026-02-18T21:31:36.638000+00:00] barnabasbusa: this does look like an older dora image possibly cached locally
[2026-02-18T21:32:09.075000+00:00] barnabasbusa: yeah consensoor can’t peer with anyone but itself (other consensoor peers)
[2026-02-18T21:32:48.811000+00:00] barnabasbusa: I have that there as a placeholder till we have the first working client
[2026-02-18T21:33:35.685000+00:00] 0xunclebill: im using `--image-download always` plus the dora params mentioned above. still seeing a few issues, but need to double check a few things on our end. will get back to you soon.
[2026-02-18T21:33:46.761000+00:00] 0xunclebill: a local LH only network seems to be working and finalizing, just need to make a few tweaks before its ready to interop with other clients
[2026-02-18T21:34:59.402000+00:00] jtraglia: Nice, congrats that's awesome!
```

</details>
