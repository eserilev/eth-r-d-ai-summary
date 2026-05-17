# EIP-7928 Integration and Merge Conflicts

---

## 2026-04-14 (epbs)

terencechain discussed the challenges of integrating EIP-7928, noting that while having two substantial additions in parallel could create merge conflicts for client implementations, the impact should be manageable since the overlapping areas appear fairly isolated. EIP-7928's changes are concentrated in specific areas: the beacon state, the payload, and the Engine API call.

terencechain requested information about a consensus-specs PR that reflects the latest progress on merging EIP-7928 into the gloas spec folder, and offered to take on this work in a day or two once the specification becomes more finalized. The message was directed to a specific team member for clarification on the current status.

**Participants:** terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-14T01:57:12.084000+00:00] terencechain: The only downside is that having two substantial additions in parallel can make merge conflicts a pain to deal with on the client side. That said, it prob will not be too bad. From a quick look, the overlapping areas seem fairly isolated. EIP-7928 appears on beacon state, the payload, and the Engine API call

Is there a consensus-specs PR that reflects the latest progress on merging EIP-7928 into the gloas spec folder? I can take this on in a day or two once the spec is a bit more finalized <@1126229231572094976>
```

</details>

