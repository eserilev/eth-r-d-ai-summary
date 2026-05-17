# P2P Colocation Whitelist Configuration and Default Enabling

---

## 2026-05-10 (epbs)

The discussion centers around configuring P2P colocation whitelist settings for Ethereum testing. terencechain suggests using the configuration `--p2p-colocation-whitelist=0.0.0.0/0,::/0` which appears to whitelist all IPv4 and IPv6 addresses, effectively allowing all connections regardless of colocation.

barnabasbusa proposes enabling this configuration by default for all future "kt runs" (likely Kurtosis test runs) and references a pull request (#1387) in the ethereum-package repository that presumably implements this change. The discussion concludes with barnabasbusa asking about additional information from assertoor logs, suggesting they are investigating test results or debugging issues related to this configuration change.

The conversation leaves open whether the proposed default enabling will be implemented and what the assertoor logs reveal about the system's behavior with these settings.

**Participants:** barnabasbusa, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-05-10T02:45:14.718000+00:00] terencechain: i think it's `--p2p-colocation-whitelist=0.0.0.0/0,::/0`
[2026-05-10T07:24:25.868000+00:00] barnabasbusa: should we just default enable this for all future kt runs?
[2026-05-10T07:25:59.473000+00:00] barnabasbusa: https://github.com/ethpandaops/ethereum-package/pull/1387
[2026-05-10T07:27:06.916000+00:00] barnabasbusa: assertoor logs telling you anything else?
```

</details>

