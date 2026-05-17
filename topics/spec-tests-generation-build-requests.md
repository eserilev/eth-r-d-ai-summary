# Spec Tests Generation and Build Requests

---

## 2026-04-16 (epbs)

jtraglia initiated spec test generation for EIP-7843, running minimal tests that include EIP-5094 and are expected to take about an hour to complete. The work is being done on a branch with an associated consensus spec PR (#4840).

sproul requested a build from the master branch without BAL (presumably referring to a specific component or feature). jtraglia confirmed this is possible and asked whether sproul wants just minimal tests or both minimal and mainnet tests, noting that minimal tests would be faster to generate.

The discussion leaves open the question of sproul's preference for the scope of the requested master branch build.

**Participants:** jtraglia, sproul

<details>
<summary>Raw messages</summary>

```
[2026-04-16T22:48:16.636000+00:00] jtraglia: Nice. I just kicked off the tests action for my eip7843 branch here:
* https://github.com/jtraglia/consensus-specs/actions/runs/24537943344
[2026-04-16T22:48:53.483000+00:00] jtraglia: Just the minimal tests. Will take ~1 hour to generate. This includes 5094 too.
[2026-04-16T22:50:24.618000+00:00] jtraglia: And this is the spec PR: https://github.com/ethereum/consensus-specs/pull/4840
[2026-04-16T23:58:01.686000+00:00] sproul: <@592004585506340885> Can we get a build from `master` without BAL?
[2026-04-16T23:59:48.895000+00:00] jtraglia: Yes! Just minimal or do you want minimal & mainnet? Just minimal would be faster.
```

</details>

---

## 2026-04-17 (epbs)

The discussion centers around the availability of reference tests for Ethereum consensus specifications. Initially, sproul indicates that minimal configuration tests would be sufficient for their needs.

jtraglia announces that reference tests for the master branch would be available within an hour, but nc1234 requests that mainnet configuration tests also be included. They explain that there was a specific issue with mainnet's proposer boost test related to pre-Gloas cutoff timing that affected mainnet but not minimal configurations, which was addressed in PR #5095.

In response to this request, jtraglia confirms that comprehensive reference tests covering general, minimal, and mainnet configurations will be available within 5 hours, addressing the need for both test environments to catch configuration-specific issues.

**Participants:** jtraglia, nc1234, sproul

<details>
<summary>Raw messages</summary>

```
[2026-04-17T00:00:13.164000+00:00] sproul: I guess just minimal would be fine yeah
[2026-04-17T00:02:26.981000+00:00] jtraglia: Reference tests for master will be available here in ~1 hour.
https://github.com/ethereum/consensus-specs/actions/runs/24540424255
[2026-04-17T00:02:28.743000+00:00] nc1234: Would be great if you can also do mainnet. Cuz there was an issue with mainnet's proposer boost test using pre-gloas cutoff time that didn't happen in minimal but mainnet. Was fixed in https://github.com/ethereum/consensus-specs/pull/5095
[2026-04-17T00:03:23.869000+00:00] jtraglia: Reference tests for everything (general/minimal/mainnet) will be available here in ~5 hours.
https://github.com/ethereum/consensus-specs/actions/runs/24540447082
```

</details>

