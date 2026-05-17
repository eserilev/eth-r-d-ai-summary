# Attestation Processing for Unknown Nodes Implementation Discussion

**Channel:** epbs | **Date:** 2026-02-13

## Summary

Potuz opened a draft PR (#4918) proposing to ignore attestations for nodes with unknown payload status in Ethereum's consensus specs. While acknowledging this could lose useful votes for distinguishing between consensus block branches, he argued it would significantly simplify implementation, particularly for Prysm's fork choice logic which tracks validator balances directly in nodes and propagates them to compute weights.

The discussion revealed that current client implementations already handle this inconsistently - Nimbus doesn't process attestations for unknown nodes, while Lodestar already discards FULL votes when execution payloads haven't been seen. Multiple developers (tersec, 0xunclebill, nc1234) expressed support for the PR, noting that the alternative would require complex refactoring including queueing mechanisms with timeouts to handle delayed payloads.

A consensus emerged favoring the simpler approach of ignoring these attestations, with all participating client teams indicating this change would align with or simplify their existing implementations rather than requiring the "nightmarish refactor" that processing unknown node attestations would entail.

## Participants

- 0xunclebill
- nc1234
- potuz
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-13T09:06:58.594000+00:00] potuz: <@520034910149410861> I opened this as draft I don't remember if we discussed this or not https://github.com/ethereum/consensus-specs/pull/4918. 
I don't really know if this makes things worse or not. On the one hand it makes it simpler to reason for us to get attestations for known forkchoice statuses. On the other hand these attestations are useful to discern between two different consensus blocks branches, even if we do not know the payload status. 

Perhaps this PR makes things strictly worse by losing those votes, but wanted to raise this none-the-less before I refactor our code to be able to count these without knowing the payload status.
[2026-02-13T10:15:49.923000+00:00] potuz: But for Prysm it would be **a lot** simpler to ignore these attestations. The reason is that we keep track of the "balance" that voted for each node directly in the node, and then we propagate back to the ancestry these balances to compute the *weight* in the usual spec meaning. If we have to count these attestations we would have to do a deep change since there's no node to add to this "balance"
[2026-02-13T12:51:54.039000+00:00] potuz: Can I get some other eyes on this? do you guys process attestations for nodes you do not know about?
[2026-02-13T12:52:12.159000+00:00] potuz: This will actually be a whole shit of a refactor for us
[2026-02-13T12:54:32.938000+00:00] tersec: no, Nimbus does not
[2026-02-13T12:54:53.925000+00:00] potuz: then how do you deal with the thing this PR is raising?
[2026-02-13T12:55:05.204000+00:00] tersec: we haven't yet, we've just started on gloas fc
[2026-02-13T12:55:10.524000+00:00] tersec: it would be ugly
[2026-02-13T12:55:46.478000+00:00] potuz: for us the weight propagation is absolutely trivial if I merge the draft PR above, and a bit of a nightmarish refactor if I don't
[2026-02-13T12:55:56.614000+00:00] potuz: perhaps I can hack it somehow, but I would prefer to merge that PR
[2026-02-13T12:56:08.226000+00:00] tersec: yes, I'm in favor of that PR too
[2026-02-13T23:04:18.857000+00:00] 0xunclebill: im in favour of this change as well
[2026-02-13T23:17:06.439000+00:00] nc1234: For Lodestar, our current fork choice implementation already throws out FULL votes if we have not seen the associated execution payload. So this PR is aligned with our implementation. 
If we decide to do the opposite, we will need to queue these votes and wait for payload to arrive (with some arbitrary timeout so we don't endlessly wait for payload or else our queue will explode eventually) and still count towards the PENDING weight in the time being
[2026-02-13T23:17:38.301000+00:00] nc1234: So it is simpler for us to go with this PR and ignore these votes
```

</details>
