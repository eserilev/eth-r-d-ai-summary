# Consensus spec tests voluntary exit bug

---

## 2026-03-18 (epbs)

Stefan Bratanov identified a bug in the consensus spec tests where the voluntary exit test case was missing the `voluntary_exit.ssz_snappy` file. However, nflaig pointed out that this issue had already been addressed in an existing pull request (#5005 on the ethereum/consensus-specs repository).

Since the bug was already being handled through the existing PR, Stefan decided to ignore the issue for now rather than duplicate efforts. The discussion highlights the challenge of keeping up with the rapid pace of development and bug fixes in the Ethereum R&D community.

**Participants:** nflaig, stefanbratanov

<details>
<summary>Raw messages</summary>

```
[2026-03-18T12:39:46.199000+00:00] stefanbratanov: <@592004585506340885> is this bug https://github.com/StefanBratanov/consensus-spec-tests/tree/master/tests/mainnet/gloas/operations/voluntary_exit/pyspec_tests/builder_voluntary_exit__success doesn't seem to have `voluntary_exit.ssz_snappy`
[2026-03-18T12:40:19.754000+00:00] nflaig: your late to the party https://github.com/ethereum/consensus-specs/pull/5005 😄
[2026-03-18T12:40:56.826000+00:00] stefanbratanov: haah you are right, I am late, can't keep up with the chat, nice one, will just ignore it for now
```

</details>

