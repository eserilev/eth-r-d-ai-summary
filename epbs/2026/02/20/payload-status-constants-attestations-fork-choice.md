# PayloadStatus constants alignment between attestations and fork-choice

**Channel:** epbs | **Date:** 2026-02-20

## Summary

jtraglia identified an inconsistency in the Ethereum consensus specifications where PayloadStatus constants use different values between attestations and fork-choice implementations. In attestations, `data.index == 0` represents `EMPTY` and `data.index == 1` represents `FULL`, but in fork-choice, `PayloadStatus(1)` is `EMPTY` and `PayloadStatus(2)` is `FULL`. This misalignment prevents reusing the same constants across both contexts.

After discussion, the team agreed to standardize the values to improve consistency. fradamt proposed a solution where `PAYLOAD_STATUS_EMPTY` would be `PayloadStatus(0)`, `PAYLOAD_STATUS_FULL` would be `PayloadStatus(1)`, and `PAYLOAD_STATUS_PENDING` would be `PayloadStatus(2)`. This change wouldn't affect the tiebreaker functionality since it only compares `EMPTY` and `FULL` values and would still privilege `FULL` appropriately.

The discussion concluded with consensus on the proposed alignment, and jtraglia committed to creating a PR to implement these changes.

## Participants

- fradamt
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-20T14:20:03.316000+00:00] jtraglia: <@755590043632140352> I find it confusing that [for attestations](https://github.com/ethereum/consensus-specs/blob/241783f4999c898e708ffab01929a282c05b68aa/specs/gloas/validator.md?plain=1#L103-L106), `data.index == 0` is `EMPTY` and `data.index == 1` is `FULL`. But [in fork-choice](https://github.com/ethereum/consensus-specs/blob/241783f4999c898e708ffab01929a282c05b68aa/specs/gloas/fork-choice.md?plain=1#L67-L68), `PayloadStatus(1)` is `EMPTY` and `PayloadStatus(2)` is `FULL`. Can we make these the same so we can re-use the constants?
[2026-02-20T14:24:04.120000+00:00] potuz: That's <@520034910149410861>'s doing, not mine, but I like Francesco's approach cause it's neatly used in the tiebreaker.
[2026-02-20T14:25:16.110000+00:00] jtraglia: You like that the values are different? Or you just like the approach in general?
[2026-02-20T14:29:13.068000+00:00] potuz: Just the order is needed, we can change them to be 0 and 1 as you want I think cause empty is not used I believe in that overload of values
[2026-02-20T14:29:35.126000+00:00] potuz: Can't check now cause I'm driving but I believe that to be true
[2026-02-20T15:34:34.231000+00:00] fradamt: I think this is fine if you prefer it? The tiebreaker it's only between `EMPTY` and `FULL` and would privilege `FULL` anyway

| `PAYLOAD_STATUS_EMPTY`              | `PayloadStatus(0)`      |
| `PAYLOAD_STATUS_FULL`                | `PayloadStatus(1)`      |
| `PAYLOAD_STATUS_PENDING`         | `PayloadStatus(2)`      |
[2026-02-20T16:10:53.774000+00:00] jtraglia: Yes, something like that would be better IMO 👍
[2026-02-20T16:11:10.807000+00:00] jtraglia: I can make a PR in a bit which does this.
```

</details>
