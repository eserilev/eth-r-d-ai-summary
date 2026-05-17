# Honest payload reorg feature specification

**Channel:** epbs | **Date:** 2026-05-02

## Summary

Potuz proposed adding an "honest payload reorg feature" to the Ethereum specification rather than leaving it as an implementation detail. This feature would enable reorganizing payloads that were attested late by the Payload Timeliness Committee (PTC), with pricing mechanisms used to prevent payload execution from extending into the next slot.

The motivation stems from current performance issues at 150M gas limit, where some payloads are taking up to 13 seconds to execute in certain cases. The proposal seeks input from another team member on formally specifying this reorg capability to address these timing concerns.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-02T14:16:39.083000+00:00] potuz: <@520034910149410861> how do you feel about putting the honest payload reorg feature in the spec to reorg payloads that were attested late by the PTC, instead of leaving it as implementation detail? 

With this we can use pricing to prevent the Payload executing into the next slot. At 150M we are seeing some Payloads taking 13 seconds in some cases
```

</details>
