# Test Vectors and LH Updates

---

## 2026-04-14 (epbs)

The discussion centers on updating Lighthouse (LH) to implement "Nico's approach," though the specific technical details of this approach are not elaborated in the message. Sproul indicates they are proceeding with this implementation update.

There is an open action item for generating test vectors from the relevant branch to validate the updated implementation. Sproul is requesting assistance from the team to build these test vectors, suggesting this is a collaborative effort needed to properly test the changes being made to Lighthouse.

**Participants:** sproul

<details>
<summary>Raw messages</summary>

```
[2026-04-14T04:58:05.461000+00:00] sproul: We are updating LH to follow _Nico's approach_. Could run test vectors if someone builds them from the branch 🙏
```

</details>

---

## 2026-04-17 (epbs)

jtraglia generated and posted a fixed test, then ran a comprehensive test suite on the latest master branch that includes a field swap change in the state. After confirming the tests work properly, they merged the test fix along with several other changes and initiated the test generation action for an upcoming release.

The merged changes include everything expected for the release except for PR #5067, which remains under debate among clients. Reference tests from this release preparation will be available through the provided GitHub Actions run, with completion expected in the near future.

**Participants:** jtraglia

<details>
<summary>Raw messages</summary>

```
[2026-04-17T17:38:17.635000+00:00] jtraglia: <@363800010518822915> I will generate the fixed test. I'll post it here in just a minute.
[2026-04-17T17:43:23.796000+00:00] jtraglia: 
[2026-04-17T17:45:21.348000+00:00] jtraglia: And then this run will contain the full suite of tests on top of the latest master:
https://github.com/jtraglia/consensus-specs/actions/runs/24578808301
[2026-04-17T17:46:11.661000+00:00] jtraglia: Note, this does include the field swap change in the state.
[2026-04-17T17:46:47.541000+00:00] jtraglia: If you can confirm this works, I'll merge the PR with the fix.
[2026-04-17T20:40:50.168000+00:00] jtraglia: Merged the test fix & a few other things. Just started the testgen action. This includes everything that's expected to go into the release except [5067](https://github.com/ethereum/consensus-specs/pull/5067) which is still being debated by clients (see https://discord.com/channels/595666850260713488/1494740617769652225). Reference tests will be available here in <t:1776476400:R>:
https://github.com/ethereum/consensus-specs/actions/runs/24585621680
```

</details>

