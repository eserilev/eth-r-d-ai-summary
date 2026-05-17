# Proposer bid ranking and relay preferences

**Channel:** epbs | **Date:** 2025-12-12

## Summary

l03443 suggests that proposer bid ranking information could be useful to include in relay preferences or in the get bid request. This would help relays and builders understand how proposers evaluate and prioritize different bids.

The discussion highlights a specific use case around preconfirmations (preconfs) protocols, where opted-in proposers must always select headers from the protocol relay and cannot fallback to local building, as doing so would breach the preconfirmation and risk slashing. Currently, this requirement is implemented in a "hacky way" by artificially inflating the bid value to ensure selection. l03443 suggests that the system could be improved to natively support this use case rather than relying on bid manipulation.

## Participants

- l03443

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T11:08:56.684000+00:00] l03443: i suppose information about how the proposer will rank bids could be useful to include as well in the preferences? or in the get bid request https://discord.com/channels/595666850260713488/692078615269212180/1448993425415344179
[2025-12-12T11:13:08.153000+00:00] l03443: some protocols like preconfs require an opted in proposer to always pick headers from the protocol relay and never fallback to local building (since this would be breaching the preconf and risk slashing), afaik this is done in a bit hacky way by adding a large value to the bid. it could also make sense to support this use case if possible
```

</details>
