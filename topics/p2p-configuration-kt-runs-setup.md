# P2P Configuration and KT Runs Setup

---

## 2026-05-10 (epbs)

The discussion centers around P2P configuration settings for Kurtosis (KT) runs, with terencechain providing the specific configuration parameter `--p2p-colocation-whitelist=0.0.0.0/0,::/0`. This setting appears to whitelist all IPv4 and IPv6 addresses for P2P colocation.

barnabasbusa suggests making this configuration the default for all future KT runs and references a GitHub pull request (#1387) in the ethereum-package repository that likely implements this change. The conversation concludes with barnabasbusa asking about additional information from assertoor logs, suggesting they are investigating or debugging some aspect of the P2P configuration setup, though no response to this inquiry is shown in the provided messages.

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

