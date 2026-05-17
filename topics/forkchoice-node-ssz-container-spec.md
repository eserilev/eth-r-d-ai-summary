# ForkChoiceNode SSZ Container specification issue

---

## 2026-05-07 (epbs)

The discussion centers around an inconsistency in the Ethereum consensus specification where `ForkChoiceNode` is defined as an SSZ `Container` but may not be intended as such. m.kalinin questioned why it has this designation, and nflaig confirmed this appears to be an oversight, noting that Lodestar doesn't implement it as an SSZ container and must skip related `ssz_static` tests as a result.

nflaig proposed fixing the issue by adding a `@dataclass` decorator to make `ForkChoiceNode` a proper dataclass rather than an SSZ container, though initially noted this change seemed to break fast-confirmation tests. m.kalinin clarified that fast-confirmation tests shouldn't be affected since they don't run on the gloas fork. nflaig subsequently opened PR #5218 to implement the fix, converting `ForkChoiceNode` from an SSZ container to a dataclass, with CI running to validate the changes.

**Participants:** m.kalinin, nflaig

<details>
<summary>Raw messages</summary>

```
[2026-05-07T08:37:20.216000+00:00] m.kalinin: why `ForkChoiceNode` is defined as an SSZ `Container` in the spec?
[2026-05-07T15:12:24.333000+00:00] nflaig: this seems like an oversight, I was always confused why it's part of `ssz_static` tests, we don't have it defined as a ssz container in lodestar so we need to skip that test

seems like we just need to add a `@dataclass` decorator, but doing that breaks fast-confirmation tests it seems
[2026-05-07T15:13:12.250000+00:00] m.kalinin: it shouldn't break fast confirmation tests as they aren't run on gloas fork tho
[2026-05-07T15:17:11.810000+00:00] nflaig: https://github.com/ethereum/consensus-specs/pull/5218 CI is running
[2026-05-07T15:24:41.758000+00:00] nflaig: Make ForkChoiceNode a dataclass instead ...
```

</details>

