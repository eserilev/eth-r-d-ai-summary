# ePBS builder slashing and withdrawability function confusion

**Channel:** epbs | **Date:** 2025-12-02

## Summary

A confusion was identified in the ePBS specifications regarding the `is_builder_slashed_and_not_withdrawable` function. The docstring states it "checks if builder is slashed AND not yet withdrawable," but the pseudocode uses `withdrawable_epoch >= current_epoch or not slashed`, which appears to use OR logic instead of AND. This discrepancy had caused confusion in previous discussions as well.

Potuz clarified that the pseudocode is mathematically correct, explaining that `withdrawable_epoch >= current_epoch or not slashed` is equivalent to the negation of `slashed and withdrawable_epoch < current_epoch`, which matches the docstring's intent. The function returns true when the builder is either withdrawable OR not slashed, which is logically equivalent to checking if it's NOT the case that the builder is both slashed and not yet withdrawable.

To address the ongoing confusion around this function, jtraglia proposed moving the explanatory comments outside the function into a note paragraph, since this particular function is more confusing than others. A pull request (#4770) was created to implement this documentation improvement.

## Participants

- jtraglia
- mushow
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-02T14:42:07.929000+00:00] mushow: Hi there, I was looking at ePBS specifications:

The docstring in links below currently says:

> "Checks if builder is slashed **AND** not yet withdrawable."

However, if we look at the pseudocode, it evaluates to `True` when:

> builder is **NOT** yet withdrawable **OR NOT** slashed

[gloas/beacon-chain](https://github.com/ethereum/consensus-specs/blob/c20c1bd2273866f4f3fa5084d0d89dc51e24c405/specs/gloas/beacon-chain.md?plain=1#L742-L750)

There seems to be a mismatch, it uses `or` in the pseudocode instead of `and`.  We want both conditions to return `True` to return `True`, but currently in the pseudo-code, we only need **one of the two** conditions to return `True` because of the `or`  keyword being used.
[2025-12-02T14:44:48.657000+00:00] mushow: 
[2025-12-02T14:45:10.968000+00:00] mushow: > L750
[2025-12-02T14:53:50.759000+00:00] jtraglia: Hmm yes, this function is confusing. I'm not sure what is correct. There was confusion about this here (https://github.com/ethereum/consensus-specs/pull/4631#discussion_r2398679255) too. cc <@755590043632140352>
[2025-12-02T15:53:55.812000+00:00] potuz: `withdrawable_epoch >= current_epoch or not slashed` is mathematically equivalent to the negation of `slashed and withdrawable_epoch < current_epoch`  which is the check in the docstring
[2025-12-02T16:47:27.206000+00:00] jtraglia: Ah yeah. Okay so I've thought about it some. Is this docstring accurate?
[2025-12-02T16:48:22.011000+00:00] jtraglia: 
[2025-12-02T16:54:17.188000+00:00] potuz: This is correct, but I believe the spec should not be an annotated spec. The current docstring is strictly accurate. If you want to add a `not` or if you want to change it to `check if the builder is withdrawable or not slashed` that is fine. Anyway, if you want to include that whole text is also fine I guess.
[2025-12-02T16:54:54.078000+00:00] potuz: I like to read code without many comments, that's just a matter of taste, others like very descriptive comments.
[2025-12-02T17:01:48.284000+00:00] jtraglia: Hmm yeah, comments can be distracting. How about I move this outside of the function and into a note paragraph. I'd like to do this because this function is a bit more confusing than others.
[2025-12-02T17:09:33.266000+00:00] mushow: okay that makes more sense now, thanks.
[2025-12-02T17:09:54.494000+00:00] jtraglia: https://github.com/ethereum/consensus-specs/pull/4770
```

</details>
