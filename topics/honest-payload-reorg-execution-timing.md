# Honest payload reorg feature and payload execution timing

---

## 2026-05-02 (epbs)

Potuz proposed adding an "honest payload reorg feature" to the official specification rather than leaving it as an implementation detail. This feature would allow reorganizations of payloads that receive late attestations from the Payload Timeliness Committee (PTC). The motivation is to use pricing mechanisms to prevent payload execution from extending into the next slot, as current observations show some payloads taking up to 13 seconds to execute at 150M gas limit.

The discussion appears to be seeking feedback on whether this should be standardized in the specification, but no response or consensus was captured in the provided messages, leaving this as an open question for further consideration.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-02T14:16:39.083000+00:00] potuz: <@520034910149410861> how do you feel about putting the honest payload reorg feature in the spec to reorg payloads that were attested late by the PTC, instead of leaving it as implementation detail? 

With this we can use pricing to prevent the Payload executing into the next slot. At 150M we are seeing some Payloads taking 13 seconds in some cases
```

</details>

