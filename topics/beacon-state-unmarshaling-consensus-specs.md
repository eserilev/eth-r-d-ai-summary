# Beacon state unmarshaling and consensus specs implementation

---

## 2026-04-18 (epbs)

terencechain reported an issue with unmarshaling beacon state data and asked whether a specific consensus specs pull request (#5113) addresses this problem. jtraglia confirmed that the PR does indeed contain the necessary changes to resolve the unmarshaling issue.

As a result, terencechain identified that they need to implement the changes from PR #5113 in their codebase to fix the beacon state unmarshaling problem they were experiencing.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-18T00:33:55.757000+00:00] terencechain: i cant even unmarhsal the beacon state for some reason, does this have? https://github.com/ethereum/consensus-specs/pull/5113 ?
[2026-04-18T00:34:43.355000+00:00] jtraglia: Yes, it does.
[2026-04-18T00:35:00.130000+00:00] terencechain: ah ok.. i need to implement that one 🙂
```

</details>

