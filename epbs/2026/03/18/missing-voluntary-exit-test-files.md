# Missing Voluntary Exit Test Files

**Channel:** epbs | **Date:** 2026-03-18

## Summary

Stefan Bratanov identified a bug in the consensus spec tests where the voluntary exit test files appear to be missing the `voluntary_exit.ssz_snappy` file in the `builder_voluntary_exit__success` test case. However, nflaig quickly pointed out that this issue has already been addressed in an existing pull request (#5005 on the ethereum/consensus-specs repository).

Stefan acknowledged that he was "late to the party" and had missed the ongoing discussion about this issue. He decided to ignore the bug for now since it's already being handled through the existing PR. No further action items were identified as the issue is already in progress through the proper channels.

## Participants

- nflaig
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-18T12:39:46.199000+00:00] stefanbratanov: <@592004585506340885> is this bug https://github.com/StefanBratanov/consensus-spec-tests/tree/master/tests/mainnet/gloas/operations/voluntary_exit/pyspec_tests/builder_voluntary_exit__success doesn't seem to have `voluntary_exit.ssz_snappy`
[2026-03-18T12:40:19.754000+00:00] nflaig: your late to the party https://github.com/ethereum/consensus-specs/pull/5005 😄
[2026-03-18T12:40:56.826000+00:00] stefanbratanov: haah you are right, I am late, can't keep up with the chat, nice one, will just ignore it for now
```

</details>
