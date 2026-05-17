# Rollback tracking and beacon API head events

---

## 2026-05-02 (epbs)

terencechain provided a suggestion for tracking rollbacks using the beacon API head event endpoint, noting that this endpoint indicates whether blocks are empty or full. This approach would be equivalent to the consensus layer calling the forkchoiceUpdated (FCU) function and obtaining the same information through the execution layer's endpoint, offering an alternative method for applications that need to monitor blockchain reorganizations.

**Participants:** terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-02T13:18:19.516000+00:00] terencechain: side comment, ppl that care about rollback could also track head event end point via beacon api that should indicates empty/full, this is the same as CL calling FCU and getting it via execution's end point
```

</details>

