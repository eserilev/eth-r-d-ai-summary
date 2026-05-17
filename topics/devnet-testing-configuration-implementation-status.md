# Devnet Testing Configuration and Implementation Status

---

## 2026-04-16 (epbs)

Terencechain sought confirmation that the Amsterdam execution API specification is the source of truth for glamsterdem devnet 0, then requested guidance on testing their Prysm implementation of balance and slot precompiles using Kurtosis. The team provided links to the bal-devnet-3 documentation and specified using the `ethpandaops/geth:bal-devnet-3` image for local testing.

Terencechain successfully implemented the balance and slot precompiles in Prysm along with payload changes for the Amsterdam engine API, confirming the implementation works with the specified Geth image and can achieve finalization in Kurtosis. They also verified that syncing functionality works correctly. The next steps involve waiting for specification tests and an official release to ensure full compliance with the test suite.

**Participants:** jtraglia, qu0b, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-16T18:09:23.977000+00:00] terencechain: may i assume https://github.com/ethereum/execution-apis/blob/main/src/engine/amsterdam.md
is the source of truth now for glamsterdem devnet 0?
[2026-04-16T18:28:38.524000+00:00] terencechain: i implemented bal and slot pre compile for prysm, i want to test it on kurtosis, which EL/geth image can i use? 
even better a kurtosis config example that worked for bal devnet will be great
[2026-04-16T18:54:26.975000+00:00] jtraglia: cc <@&1460290804898861117>
[2026-04-16T18:54:59.876000+00:00] qu0b: <https://notes.ethereum.org/@ethpandaops/bal-devnet-3>
[2026-04-16T18:57:50.358000+00:00] jtraglia: Thanks! Specifically: https://notes.ethereum.org/@ethpandaops/bal-devnet-3#Local-testing
[2026-04-16T18:58:05.120000+00:00] jtraglia: `ethpandaops/geth:bal-devnet-3`
[2026-04-16T22:10:19.175000+00:00] terencechain: quick update: prysm implemented bal, slot in payload changes with amsterdam engine api and can finalize kurtosis with `ethpandaops/geth:bal-devnet-3`. I tested syncing as well 
next step is to wait for spec test and a release to make sure we can pass those
```

</details>

---

## 2026-04-17 (epbs)

In this brief technical discussion, barnabasbusa initially inquired about the availability of a branch for development work. They then announced their intention to deploy or reset "epbs-devnet-1," suggesting this is related to Ethereum's enshrined Proposer-Builder Separation (ePBS) development efforts.

To assist with the devnet setup, barnabasbusa directed others to reference the Glamsterdam devnet-0 documentation at https://notes.ethereum.org/@ethpandaops/glamsterdam-devnet-0, suggesting team members should review this resource to ensure they haven't missed any important configuration or setup requirements for their devnet work.

**Participants:** barnabasbusa

<details>
<summary>Raw messages</summary>

```
[2026-04-17T14:49:18.099000+00:00] barnabasbusa: got a branch ?
[2026-04-17T14:58:13.730000+00:00] barnabasbusa: I'm gonna yeet epbs-devnet-1
[2026-04-17T15:14:35.524000+00:00] barnabasbusa: Try to take a look at: https://notes.ethereum.org/@ethpandaops/glamsterdam-devnet-0 and see if you missing anything
```

</details>

