# Validator index vs pubkey performance considerations

**Channel:** epbs | **Date:** 2025-12-12

## Summary

The discussion centers on a trade-off between using `validator_index` versus pubkey in Ethereum validator operations. Tersec initially suggested that using `validator_index` could help with proof of possession efficiency, but raised concerns that it might make other signature verification operations less efficient, potentially creating a "false economy."

Potuz clarified that the specific helpers being discussed don't actually perform signature verification, and that the change stems from modifications to builder pending payments, which are never signed. He concluded that using `validator_index` in this context would save zero lookups while introducing at least one additional lookup per payment, suggesting the change would be counterproductive rather than beneficial.

## Participants

- potuz
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T18:57:40.855000+00:00] tersec: regarding this, https://discord.com/channels/595666850260713488/874767108809031740/1442324025266143284

i.e. using a `validator_index` helps with proof of possession
[2025-12-12T18:58:31.640000+00:00] tersec: if this makes other signature verification less efficient, it might be a false economy
[2025-12-12T19:05:37.219000+00:00] potuz: This doesn't make any signature verification easier since these helpers don't verify any signature
[2025-12-12T19:06:23.042000+00:00] tersec: those helpers don't, no
[2025-12-12T19:06:28.678000+00:00] tersec: but other things use that field
[2025-12-12T19:07:22.040000+00:00] potuz: So this stems from changing in the builder pending payment which is never signed so I think this will save zero lookups
[2025-12-12T19:07:48.070000+00:00] tersec: ah ok
[2025-12-12T19:07:49.426000+00:00] potuz: And introduces at least one per payment
```

</details>
