# Late payload timing and centralization effects

**Channel:** epbs | **Date:** 2025-12-19

## Summary

This discussion explores the effects of late payload timing in Ethereum's proposed ePBS (enshrined Proposer-Builder Separation) system. The key concern raised is that when builders release payloads late, it affects pipelining by giving proposers and attesters less time to execute the payload. Additionally, late payloads may create centralization advantages for builders who can monopolize information about their payload's post-state, potentially making them more likely to win subsequent auctions - though this centralization vector already exists in the current system.

The conversation then shifts to comparing late payload handling in ePBS versus current late block reorg mechanisms. While the current system allows proposers to reorg late blocks (with all clients enabling this feature), there's debate about whether ePBS should force the next proposer to reorg or maintain the current optional approach. Potuz argues that preserving liveness is more important than addressing the centralization vector, while m.kalinin suggests that builders could be blamed for consistently publishing late payloads.

A key technical distinction emerges: late blocks affect consensus while late payloads do not, and ePBS decouples execution layer (EL) liveness from consensus layer (CL) liveness. The discussion notes that late block reorgs are only enabled with recent finalization to avoid liveness issues, and concludes that the debate may become moot if reorg-by-default is implemented, though some forks might implement it regardless.

## Participants

- m.kalinin
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-19T10:34:11.358000+00:00] m.kalinin: If builder releases a payload later then the pipelining will be affected because proposer and attesters will have less time to execute payload. What other adverse effects there can be?
[2025-12-19T10:36:23.360000+00:00] potuz: It also may affect next builders, giving the delaying builder advantage of information by monopolizing the post state of it's own payload and making it more likely to win the next auction. But I think this exist today in the exact same form
[2025-12-19T10:40:09.711000+00:00] m.kalinin: It makes sense to me to improve on monopolizing to reduce the centralization vector
[2025-12-19T10:41:08.973000+00:00] potuz: Yeah but I don't buy this is a meaningful vector. And preserving liveness in the event of late blocks seems much more important
[2025-12-19T10:48:36.609000+00:00] m.kalinin: We're reorging late blocks today which isn't too bad for liveness. Also it will be possible to blame specific builders for publishing payloads late if they do it constantly
[2025-12-19T10:57:18.075000+00:00] potuz: We aren't reorging them: we allow the proposer to reorg then if they want. The question is if we want to do it the same in ePBS or we want to switch to forcing the next proposer to reorg
[2025-12-19T10:57:52.038000+00:00] potuz: I'm not sure there's a strong case to forcing the next proposer
[2025-12-19T11:13:30.835000+00:00] m.kalinin: I believe late block reorgs are enabled by all clients today. If this is true, then late block reorgs are effectively happening even though proposers are not enforced to do it today and there doesn't seem to be serious liveness issues because of that
[2025-12-19T11:16:20.666000+00:00] potuz: The other difference is that late blocks affect consensus, late payloads don't
[2025-12-19T11:18:00.166000+00:00] potuz: Anyway this whole thing may be a mute argument if we implement reorg by default
[2025-12-19T11:18:15.052000+00:00] potuz: If we don't, we may see forks that do
[2025-12-19T12:58:40.830000+00:00] nflaig: late block reorg is only enabled if we have recent finalization, so it shouldn't be a liveness issue?
[2025-12-19T13:01:06.378000+00:00] m.kalinin: Good point. I believe we were talking about EL liveness above, not a CL liveness. ePBS makes these two things decoupled
```

</details>
