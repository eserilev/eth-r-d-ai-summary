# Builder bid validation and fee recipient checks

**Channel:** epbs | **Date:** 2025-12-17

## Summary

nflaig highlighted the need for better documentation around builder bid validation, particularly noting that making fee recipient checks optional would be beneficial given that the current specification's `fee_recipient` check is broken. They pointed out that clients are already implementing additional validation checks beyond what's specified in `process_bid`, such as `gas_limit` validation, but these implementations vary significantly between clients.

The inconsistency in client behavior is problematic, with some clients performing strict exact equality checks while others use more lenient validation that simply ensures the gas limit moves in the correct direction. This suggests a need for standardization of these validation behaviors across different client implementations.

## Participants

- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-17T10:46:34.311000+00:00] nflaig: It would be good to have this documented, having it optional sounds good (also considering current spec `fee_recipient` check is broken), and clients implement additional checks right like `gas_limit` which is not part of `process_bid` in the spec, and I believe the behavior of this check is not the same, some are more strict (do exact equality) while others just make sure it moves the gas limit in the right direction (that's what we do)
```

</details>
