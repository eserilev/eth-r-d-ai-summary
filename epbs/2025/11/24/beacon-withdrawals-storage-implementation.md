# Withdrawals in state implementation and spec tests

**Channel:** epbs | **Date:** 2025-11-24

## Summary

The discussion centers around an implementation change by Etan related to "withdrawals in state" functionality. Potuz inquires whether Teku (an Ethereum consensus client) supports this particular change.

Stefan Bratanov confirms that Teku has implemented support for the withdrawals in state feature and has it ready in a pending pull request. However, the merge is blocked pending the availability of specification tests for this functionality, indicating that proper test coverage is required before the implementation can be finalized.

## Participants

- potuz
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-11-24T09:43:42.543000+00:00] potuz: By Etan's change you mean the withdrawals in the state? I take it then the Teku supports this?
[2025-11-24T14:26:47.190000+00:00] stefanbratanov: Yeah, I meant that one. Have it in a pending PR but will merge when there are specs tests for it.
```

</details>
