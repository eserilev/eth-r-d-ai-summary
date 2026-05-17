# Builder Withdrawal Delay PR and Configuration

**Channel:** epbs | **Date:** 2026-05-08

## Summary

jtraglia proposed a PR (#5223) to raise the minimum builder withdrawal delay in the Ethereum consensus specifications, viewing it as a straightforward change that should be implemented regardless of EIP-7688. However, he noted that implementing this change would require introducing a new configuration value, which adds complexity to the implementation.

barnabasbusa suggested this could be an opportune time to push for EIP-7688 instead. After considering the configuration overhead, jtraglia decided to take more time to think through the approach, noting there's no immediate urgency for the change. The discussion remains open with no final decision on whether to proceed with the standalone PR or tie it to the broader EIP-7688 effort.

## Participants

- barnabasbusa
- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T14:48:42.357000+00:00] jtraglia: A PR to raise the min builder withdrawal delay, something I think we should do regardless:
* https://github.com/ethereum/consensus-specs/pull/5223
[2026-05-08T14:50:52.403000+00:00] jtraglia: It's such a simple change. I think we should do this independent of 7688.
[2026-05-08T14:51:21.072000+00:00] barnabasbusa: good time to push for 7688 😄
[2026-05-08T14:51:22.262000+00:00] jtraglia: But then again, it would require a new config value, which is annoying.
[2026-05-08T14:51:30.591000+00:00] jtraglia: Let me think about it more. There's no rush for this one.
```

</details>
