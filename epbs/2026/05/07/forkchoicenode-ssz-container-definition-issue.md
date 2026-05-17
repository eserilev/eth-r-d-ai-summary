# ForkChoiceNode SSZ Container Definition Issue

**Channel:** epbs | **Date:** 2026-05-07

## Summary

The discussion centers on a specification inconsistency where `ForkChoiceNode` is defined as an SSZ `Container` in the Ethereum consensus spec, which appears to be an oversight. This has caused confusion for implementers, with Lodestar not defining it as an SSZ container and consequently needing to skip related `ssz_static` tests.

nflaig identified that adding a `@dataclass` decorator would resolve the issue but initially encountered problems with fast-confirmation tests. However, m.kalinin clarified that this shouldn't cause issues since those tests aren't run on the gloas fork. nflaig subsequently created PR #5218 to address the problem by making `ForkChoiceNode` a proper dataclass, with CI currently running to validate the fix.

## Participants

- m.kalinin
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-07T08:37:20.216000+00:00] m.kalinin: why `ForkChoiceNode` is defined as an SSZ `Container` in the spec?
[2026-05-07T15:12:24.333000+00:00] nflaig: this seems like an oversight, I was always confused why it's part of `ssz_static` tests, we don't have it defined as a ssz container in lodestar so we need to skip that test

seems like we just need to add a `@dataclass` decorator, but doing that breaks fast-confirmation tests it seems
[2026-05-07T15:13:12.250000+00:00] m.kalinin: it shouldn't break fast confirmation tests as they aren't run on gloas fork tho
[2026-05-07T15:17:11.810000+00:00] nflaig: https://github.com/ethereum/consensus-specs/pull/5218 CI is running
[2026-05-07T15:24:41.758000+00:00] nflaig: Make ForkChoiceNode a dataclass instead ...
```

</details>
