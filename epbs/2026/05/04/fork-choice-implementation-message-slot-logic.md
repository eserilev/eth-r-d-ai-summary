# Fork-choice and validator specification discussion

**Channel:** epbs | **Date:** 2026-05-04

## Summary

The discussion centers around where to implement a helper function in the Ethereum consensus specifications - either in the fork-choice spec or validator spec. Potuz suggests adding a helper to the fork-choice specification (similar to `should_build_on_full` from their DA PR) that would be called from the validator.md file, following the same pattern as their existing data availability PR.

However, there appears to be a dependency issue blocking progress. Potuz's DA-related PR (#5186) is waiting for another contributor's PR to be resolved before they can determine how to proceed with the implementation approach. The incomplete message from pk910 suggests there may be additional considerations about specific cases, but the full context of their input is cut off.

## Participants

- fradamt
- pk910
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-04T09:20:44.396000+00:00] fradamt: meaning in the fork-choice spec or in the validator spec?
[2026-05-04T11:20:34.654000+00:00] pk910: @pk910  i believe in case the
[2026-05-04T11:32:29.459000+00:00] potuz: I was thinking on adding the helper to forkchoice (something like should_build_on_full in my DA PR that can be updated for this) but then that helped is only called from the validator.md file. Just as my PR for DA that is still waiting for <@534934855113506836> PR to decide how to proceed. 
https://github.com/ethereum/consensus-specs/pull/5186
```

</details>
