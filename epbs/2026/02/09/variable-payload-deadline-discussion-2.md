# PTC Deadline Based on Execution Gas vs Payload Size

**Channel:** epbs | **Date:** 2026-02-09

## Summary

The discussion centers on refining PTC (Payload Timeliness Committee) voting mechanisms by making deadlines more nuanced. Potuz proposes accounting for both payload size and execution gas usage in deadline calculations, noting these changes would be trivial to implement in Prysm. The current approach uses linear interpolation based on data size, but could be enhanced to include gas consumption metrics.

A key debate emerges between two approaches: anderselowsson argues for basing deadlines primarily on execution gas, reasoning that the ultimate goal is ensuring payloads can execute before slot boundaries regardless of size. This approach would treat payloads with equal gas consumption the same way and could be more relevant for future payload chunking scenarios. Potuz counters that payload size is more important for determining adequate broadcast time, emphasizing that allowing builders to withhold payloads longer (even with sufficient execution time) creates problematic free option and centralization issues for preconfirmations.

The conversation concludes without a firm decision, with anderselowsson acknowledging that size-based measurement is more "fair" while noting that execution gas accounting could be added as a future option if builders attempt to game the system by purchasing filler calldata to artificially delay deadlines.

## Participants

- anderselowsson
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-09T20:34:52.744000+00:00] potuz: we want to make PTC voting more nuanced by accounting for size in the payload, I think we can trivially also make it a function of the gas used and the gas limit in the block
[2026-02-09T20:35:12.471000+00:00] potuz: these changes are trivial at least for Prysm
[2026-02-09T20:36:07.562000+00:00] potuz: and it allows us to set the right linear interpolation using both execution gas and data size of the block, but as long as the gas limit is such that we are still safe on Gloas, I do not see any problem with the original approach of only setting the deadline linearly on size
[2026-02-09T20:56:46.395000+00:00] anderselowsson: Conceptually, we could base the deadline only on execution gas. So a payload with a small and a large data size would have the same deadline if they consume an equal amount of execution gas. 

In a payload chunking scenario, it will seemingly become more important to do "execution gas accounting" on the chunks, while including data on their timing as well. So we could pivot to this form of measurement already now. Another thing: under distributed payload propagation, the pre-propagated transactions would likely count as having close-to-zero calldata as far as the PTC deadline is concerned.
[2026-02-09T20:58:04.242000+00:00] potuz: But the deadline is about the broadcast time, not the execution time.
[2026-02-09T20:59:48.835000+00:00] anderselowsson: Isn't it implicitly about execution time right now? And by making it explicit we increase the accuracy of our measurement?
[2026-02-09T21:03:58.338000+00:00] potuz: I don't think I follow, the plan is to make it explicitly about broadcast time, the smallest the payload the smallest the deadline, this requires only payload size and not gas usage. We could use gas_used if we also want to leverage execution time.
[2026-02-09T21:07:17.102000+00:00] anderselowsson: My point would be that at the end of the day, we use the PTC vote to ensure that the payload was propagated in such a way that it can be executed before the slot boundary. In this case, we do not actually need to concern ourselves with the size of the payload, only with arrival time and the execution gas it is set to consume (and if the builder specified incorrectly, it is of course invalid).
[2026-02-09T21:22:06.974000+00:00] anderselowsson: The PTC would however still require the payload to have arrived by the time it votes. That becomes the latest arrival point regardless.
[2026-02-09T21:22:13.363000+00:00] potuz: I think size is more important and bound that the broadcast time was sufficient and not much more, otherwise the effects of allowing the builder to withhold for longer, even though the time alloted for execution was more than fine, are worse (free option problems, centralization problems for preconfirmations, etc)
[2026-02-09T21:27:00.107000+00:00] anderselowsson: Yeah it is more "fair" to go by size. Not sure what is best. We can have it as an option to account for execution gas in the future if we go for chunking or if it turns out that some builders buy filler calldata to delay the deadline anyway (which would motivate measuring by execution gas)
```

</details>
