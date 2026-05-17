# ePBS beacon block API changes and execution payload handling

**Channel:** epbs | **Date:** 2025-11-28

## Summary

Bharath Vedartham raised a question about how the beacon block API will change with ePBS (enshrined Proposer-Builder Separation), noting that beacon blocks will only contain execution payload bids rather than full execution payloads. They asked whether the existing beacon block API would return blocks with just the bid, while requiring a separate API endpoint for the actual execution payload envelope.

Stefan Bratanov confirmed this approach, explaining that there will indeed be a separate API to retrieve the payload. He directed bharath to an active pull request (ethereum/beacon-APIs/pull/552) that implements these API changes for ePBS support.

## Participants

- bharath.vedartham
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-11-28T10:29:25.200000+00:00] bharath.vedartham: With ePBS, the beacon block would not have the full execution payload attached to it but only the bid. I am curious how the beacon-api to get the beacon block would change with this. I assume the API to get the beacon block would just return the beacon block with the execution payload bid and we have a separate api to return the execution payload envelope?
[2025-11-28T11:26:45.740000+00:00] stefanbratanov: Yes, there would be a separate API to get the payload, there is a PR for these changes https://github.com/ethereum/beacon-APIs/pull/552
[2025-11-28T13:48:10.483000+00:00] bharath.vedartham: ahh awesome! thanks for pointing me to this!
```

</details>
