# Geth Image and RPC Method Issues

**Channel:** epbs | **Date:** 2026-03-26

## Summary

The discussion centers around technical issues with the Ethereum Geth client image and RPC method compatibility. Pawandhananjay initially asks for confirmation about whether `ethpandaops/geth:epbs-devnet-0` is still the correct Geth image to use.

The main technical issue involves an RPC method version mismatch, where pawandhananjay encounters an error stating `"newPayloadV4 must not be called for amsterdam payloads, use newPayloadV5"`. This suggests that for Amsterdam testnet payloads, the newer `newPayloadV5` RPC method should be used instead of `newPayloadV4`. The user is uncertain whether this is a Lighthouse (consensus client) or Geth (execution client) configuration issue and seeks clarification on the correct RPC method to use.

The discussion appears incomplete as barnabasbusa's message is cut off, leaving the questions about the correct Geth image and RPC method resolution unresolved. The error indicates a version compatibility issue between the consensus and execution layer clients in the Ethereum stack.

## Participants

- barnabasbusa
- pawandhananjay

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-26T18:42:33.187000+00:00] pawandhananjay: Is `ethpandaops/geth:epbs-devnet-0` still the correct geth image right now?
[2026-03-26T19:22:35.895000+00:00] pawandhananjay: I keep getting `"newPayloadV4 must not be called for amsterdam payloads, use newPayloadV5"`. not sure if its a ligthouse issue or geth issue. anyone knows what's the correct rpc method?
[2026-03-26T21:43:55.925000+00:00] barnabasbusa: I keep getting `"newPayloadV4 must not
```

</details>
