# Builder slashing and payload withholding attacks

**Channel:** epbs | **Date:** 2025-12-17

## Summary

The discussion centers on potential attack vectors in Ethereum's builder system following PR #4788, which apparently removes slashing mechanisms for builders while maintaining a minimal 1 ETH stake requirement. Povilas8490 raised concerns about malicious builders being able to spam winning bids and withhold payloads without facing slashing penalties, questioning whether this could lead to network finality issues at relatively low cost.

Jtraglia responded that such attacks would quickly become prohibitively expensive, noting that similar payload withholding attacks (using empty payloads) are already possible under the current system but are economically deterred by costs that scale with continued malicious behavior.

## Participants

- jtraglia
- povilas8490

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-17T19:53:55.367000+00:00] povilas8490: When builders cannot be slashed or penalized (according to this [PR](https://github.com/ethereum/consensus-specs/pull/4788)) with builder stake being just 1 ETH, what's the mitigation against malicious builders spamming winning bids every slot and withholding payloads (apart from balance deduction)? Can this cause non-finality events for relatively few ETH?
[2025-12-17T20:16:35.976000+00:00] jtraglia: It will quickly become very costly. You could do this today but with empty payloads.
```

</details>
