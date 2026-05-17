# Builder Preferences Container Implementation

**Channel:** epbs | **Date:** 2025-12-18

## Summary

bharath.vedartham shared an implementation proposal for a `BuilderPreferences` container in the Ethereum builder specifications. The implementation introduces a new container structure with an `execution_payment_accepted` boolean field that gets embedded within a `ValidatorRegistrationV2` container alongside existing fields like `builder_index`, `validator_index`, `fee_recipient`, `proposal_slot`, and `gas_limit`.

The design is intentionally extensible, allowing for future enhancements to the `BuilderPreferences` struct with additional options such as minimum bid requirements. This appears to be part of ongoing work on PR #138 in the ethereum/builder-specs repository to enhance validator registration capabilities with builder-specific preferences.

## Participants

- bharath.vedartham

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-18T06:30:18.604000+00:00] bharath.vedartham: https://github.com/ethereum/builder-specs/pull/138/files#diff-221ca7d5a71aa06ecab51f0872030eb8ba2217afd79b1dd24f4987e0282b9004R68 i have a `BuilderPreferences` container in the `ValidatorRegistrations` for the builder-specs. like:
```python
class BuilderPreferences(Container):
    execution_payment_accepted: boolean
```
which makes the `ValidatorRegistration` look like:
```python
class ValidatorRegistrationV2(Container):
    builder_index: BuilderIndex 
    validator_index: ValidatorIndex
    fee_recipient: ExecutionAddress
    proposal_slot: Slot
    gas_limit: uint64
    builder_preferences: BuilderPreferences
```
we should be able to extend the BuilderPreferences struct with more options like min-bid etc if needed
```

</details>
