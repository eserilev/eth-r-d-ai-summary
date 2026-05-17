# Fork-choice equivocation handling implementation

**Channel:** epbs | **Date:** 2025-12-30

## Summary

fradamt opened a PR (#4807) for handling equivocations in fork-choice, with the main change being modifications to `is_head_weak` to count equivocation weight from the committee. This ensures monotonic output - if a proposer sees a non-weak head, validators should too, since equivocations won't reduce its weight for `is_head_weak` purposes.

potuz raised performance concerns about the implementation, noting that the changes would transform `is_head_weak` from an O(1) operation (currently using tracked node weights) to one requiring full committee lookup and slashed indices checking. This could significantly impact performance since `on_attester_slashing` is called over the wire.

fradamt acknowledged the concern but noted this only applies to the immediately prior slot, not arbitrary committees, and suggested tracking equivocation weight as slashings are recorded. The discussion concluded with potuz agreeing to attempt an implementation using the head state to grab committees, despite uncertainty about the performance impact, to test the feasibility of the approach.

## Participants

- fradamt
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-30T14:35:27.532000+00:00] fradamt: Just opened this PR about handling equivocations in the fork-choice plus some small stuff: https://github.com/ethereum/consensus-specs/pull/4807. It is all explained there, and anyway mostly based on what I had discussed here https://discord.com/channels/595666850260713488/874767108809031740/1399868051280625694. 

The main difference from   is that `is_head_weak` counts the weight of equivocations from the committee, to make its output monotonic (if the proposer sees a *not* weak head, validators should too, equivocations will not reduce its weight as far as `is_head_weak` is concerned)
[2025-12-30T15:58:06.629000+00:00] potuz: Looking at these changes, I know we discussed them before. I wonder how effective the changes to `is_head_weak` actually is. The problem is that it changes a function that is O(1) to get the node's weight, to now get the full committee at the slot and check against the slashed indices at the time of calling, since `on_attester_slashing` is called over the wire. I am afraid that the implementation performance (not so much complexity) is a much worse. Today we track the weight on nodes so a call to this function is trivial. But now this helper needs access to the committee for the slot and that is typically a problem
[2025-12-30T16:08:32.877000+00:00] fradamt: This is only ever needed for the immediately prior slot though, not arbitrary committees. One could keep the count of equivocation weight as slashings are recorded (`on_attester_slashing`) as well I guess. I am not sure of a good way to deal with proposer equivocation otherwise. If we require proposers to not extend weak equivocations, we have to make sure that it's not easy to go from not weak to weak
[2025-12-30T16:24:32.895000+00:00] potuz: Yeah I understand the reasoning behind preventing that helper from going from False to True, I just don't see how to implement that function right now. Perhaps I'm exagerating though. In principle this can be done with the head state and we can just grab the committee from head. I'll take a shot at implementing this and see if it adds a lot
```

</details>
