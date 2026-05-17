# SSZ Blocks vs EIP-8237 Technical Debt Discussion

**Channel:** epbs | **Date:** 2026-05-13

## Summary

arnetheduck expressed concerns about EIP-8237 introducing technical debt on both the consensus layer (CL) and execution layer (EL) sides. They noted that execution layer teams have shown surprising support for SSZ blocks (EIP-7807) as a superior alternative that provides the same benefits as EIP-8237, including the ability to remove the EL-CL link, but without creating technical debt.

The discussion highlighted that Nimbus can achieve independent syncing for their goals without requiring EIP-8237, instead accomplishing this by importing RLP directly in the consensus layer and performing necessary checks there. This suggests there may be alternative implementation paths that avoid the technical debt concerns while still achieving the desired functionality.

## Participants

- arnetheduck

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-13T08:14:31.604000+00:00] arnetheduck: my general feeling about this approach is that it introduces technical debt as-is both on the cl and el side - from discussions with EL:s, they were surprisingly supportive of SSZ blocks (https://eips.ethereum.org/EIPS/eip-7807) which is superior because it provides the same benefit (and many others) as 8237 _without_ introducing said debt, ie 7807 similarly allows us to remove the EL-CL link, but in a clean way. 

nimbus does not need 8237 for gloas to sync indepdently, ie we achieve the same result by importing rlp in the the CL and performing the necessary check there
```

</details>
