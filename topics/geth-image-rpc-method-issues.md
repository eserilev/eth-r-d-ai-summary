# Geth Image and RPC Method Issues

---

## 2026-03-26 (epbs)

Pawandhananjay inquired about whether the `ethpandaops/geth:epbs-devnet-0` Docker image is still the correct version to use for current development work. They also reported encountering an RPC method compatibility issue where they're receiving an error message stating that `"newPayloadV4 must not be called for amsterdam payloads, use newPayloadV5"`. They're uncertain whether this is a Lighthouse client issue or a Geth issue and are seeking clarification on the correct RPC method to use.

Barnabasbusa appeared to start responding with a similar error message (`"newPayloadV4 must not..."`), but their message was cut off, leaving the discussion without a clear resolution or answer to the technical questions raised.

**Participants:** barnabasbusa, pawandhananjay

<details>
<summary>Raw messages</summary>

```
[2026-03-26T18:42:33.187000+00:00] pawandhananjay: Is `ethpandaops/geth:epbs-devnet-0` still the correct geth image right now?
[2026-03-26T19:22:35.895000+00:00] pawandhananjay: I keep getting `"newPayloadV4 must not be called for amsterdam payloads, use newPayloadV5"`. not sure if its a ligthouse issue or geth issue. anyone knows what's the correct rpc method?
[2026-03-26T21:43:55.925000+00:00] barnabasbusa: I keep getting `"newPayloadV4 must not
```

</details>

