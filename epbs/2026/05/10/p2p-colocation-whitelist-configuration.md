# P2P colocation whitelist configuration

**Channel:** epbs | **Date:** 2026-05-10

## Summary

The discussion centers around configuring a P2P colocation whitelist for Ethereum nodes. Terencechain suggests using the configuration `--p2p-colocation-whitelist=0.0.0.0/0,::/0`, which would whitelist all IPv4 and IPv6 addresses, effectively disabling colocation restrictions.

Barnabasbusa proposes making this configuration the default for all future "kt runs" (likely Kurtosis test runs) and references a GitHub pull request (#1387) in the ethereum-package repository that presumably implements this change. The discussion appears to be focused on testing infrastructure and whether to standardize this permissive whitelist configuration.

An open question remains about what additional information might be available from assertoor logs, suggesting there may be ongoing debugging or validation efforts related to this configuration change.

## Participants

- barnabasbusa
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-10T02:45:14.718000+00:00] terencechain: i think it's `--p2p-colocation-whitelist=0.0.0.0/0,::/0`
[2026-05-10T07:24:25.868000+00:00] barnabasbusa: should we just default enable this for all future kt runs?
[2026-05-10T07:25:59.473000+00:00] barnabasbusa: https://github.com/ethpandaops/ethereum-package/pull/1387
[2026-05-10T07:27:06.916000+00:00] barnabasbusa: assertoor logs telling you anything else?
```

</details>
