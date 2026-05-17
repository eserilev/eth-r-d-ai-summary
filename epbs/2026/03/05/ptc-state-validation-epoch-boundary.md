# PTC state validation at epoch boundary

**Channel:** epbs | **Date:** 2026-03-05

## Summary

This discussion centers on a specification question about PTC (Payload Timeliness Committee) state validation at epoch boundaries. nflaig raised concerns about which state clients should use when calling `get_ptc(state, slot)` at epoch transitions, noting that while their implementation caches PTC for all slots in an epoch and shifts current to previous at the boundary, the spec appears to require validating slot N blocks (containing slot N-1 payload attestations) using the head state after epoch transition. This could potentially require re-computing the previous-epoch PTC for that slot with updated balances.

Potuz responded suggesting that the PTC should be independent of this issue, since it's determined by the beacon committee which is fixed well in advance of the validation. However, the discussion appears to leave the core specification question unresolved, with no clear consensus on the correct implementation approach for PTC state validation at epoch boundaries.

## Participants

- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T13:30:05.671000+00:00] nflaig: spec question: what state should clients use for `get_ptc(state, slot)` at epoch boundary? We cache PTC for all slots in an epoch and shift current to previous at epoch transition. But spec seems to require validating slot N block (with slot N-1 payload attestations) using  head state after epoch transition. If so, previous-epoch PTC may need to be re-computed for that slot with new balances
[2026-03-05T13:39:14.110000+00:00] potuz: The PTC should be independent of this isn't it? it's fixed by the beacon committee that is fixed way before.
```

</details>
