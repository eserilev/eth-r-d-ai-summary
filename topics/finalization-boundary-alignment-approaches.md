# Alternative approach for finalization boundary alignment

---

## 2026-04-10 (epbs)

Potuz proposed an alternative approach for finalization boundary alignment that would require only minimal changes to the consensus mechanism. The approach would modify the process for justification and finalization while keeping the existing checkpoint structure intact, potentially allowing for full justification and proper finalization boundary alignment.

However, potuz quickly identified a critical flaw in this approach - it would prevent the system from counting attestations for unknown branches. This limitation appears to have caused potuz to abandon the proposed solution, as indicated by the "Nevermind this" follow-up message. No alternative solutions or next steps were discussed in this brief exchange.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-10T11:26:21.962000+00:00] potuz: I realized that there's a minimal change that allows to keep all of consensus without changing the checkpoint structure and it allows us to justify full and thus have finalization boundary aligned. It's just a minimal change to process justification and finalization. I'll have a PR ready today to show as an alternative
[2026-04-10T12:24:23.196000+00:00] potuz: Nevermind this, we wouldn't be able to count attestations for unknown branches 😦
```

</details>

