# Prysm Implementation Testing and Kurtosis Configuration

**Channel:** epbs | **Date:** 2026-04-12

## Summary

terencechain reported successful implementation of consensus-specs PR #5094 in Prysm, achieving finalization with Kurtosis using multiple beacon nodes. While the implementation appears to comply with the specification, they identified some new performance bottlenecks on the Prysm side during testing.

An open question remains regarding Kurtosis configuration, specifically whether there are existing config examples that can be used to generate Execution Layer (EL) requests for further testing scenarios.

## Participants

- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-12T14:24:13.033000+00:00] terencechain: i implemented https://github.com/ethereum/consensus-specs/pull/5094 for prysm and got it to finalized with kurtosis with multiple beacon nodes. i noted some new prysm side bottlenecks with this change, but as of spec, it looks good with me. 

do we have a kurtosis config example somewhere that can be used to generate EL requests?
```

</details>
