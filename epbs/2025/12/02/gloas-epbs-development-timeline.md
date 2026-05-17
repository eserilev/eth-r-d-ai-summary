# Naming inconsistency between electra and gloas fields

**Channel:** epbs | **Date:** 2025-12-02

## Summary

jtraglia raised a naming consistency issue between Electra and Gloas field names. Specifically, the Electra fields follow a naming pattern that starts with "pending" (e.g., `pending_deposits`, `pending_partial_withdrawals`), while the corresponding Gloas fields use a different pattern with "pending" at the end (`builder_pending_payments`, `builder_pending_withdrawals`).

This appears to be an observation about inconsistent naming conventions across these Ethereum protocol updates, though no resolution or action items were discussed in this brief message.

## Participants

- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-02T21:28:00.625000+00:00] jtraglia: It sort of bothers me that `builder_pending_payments` and `builder_pending_withdrawals` are inconsistent with electra fields (`pending_deposits`, `pending_partial_withdrawals`, ...). The electra fields start with "pending" and the gloas ones do not.
```

</details>
