# PAYLOAD_TIMELY_THRESHOLD Definition Issue

---

## 2026-03-31 (epbs)

0xpatches identified an issue with the definition of `PAYLOAD_TIMELY_THRESHOLD` in Ethereum's GLOAS specification. While the constant is referenced in both EIP-7732 and potuz's annotated fork-choice specification (specifically in the `is_payload_timely` function and PTC equivocations section), it lacks proper definition and clarity around how the value should be determined - whether it's client-configured or spec-defined.

Upon further investigation, 0xpatches found that the consensus specs do define `PAYLOAD_TIMELY_THRESHOLD` as `PTC_SIZE // 2`, but noted this definition "makes little sense." barnabasbusa agreed that having a separate, clearer definition would be beneficial.

The discussion highlights a documentation and specification clarity issue that needs to be addressed to properly define this threshold parameter and its determination method.

**Participants:** 0xpatches, barnabasbusa

<details>
<summary>Raw messages</summary>

```
[2026-03-31T14:38:30.623000+00:00] 0xpatches: idk if this is well known but the EIP and potuz's annotated spec make reference to PAYLOAD_TIMELY_THRESHOLD without defining it anywhere. I think some language around how the value is decided would be appropriate (client-configured? spec-defined?)

<https://www.potuz.net/posts/gloas-annotated-forkchoice/> refers to it in `is_payload_timely` and <https://eips.ethereum.org/EIPS/eip-7732> refers to it in the `PTC equivocations` section
[2026-03-31T14:40:25.400000+00:00] 0xpatches: the spec itself does define it here <https://github.com/ethereum/consensus-specs/blob/master/specs/gloas/fork-choice.md#constants>
[2026-03-31T14:40:49.843000+00:00] 0xpatches: but as `PTC_SIZE // 2` which makes little sense
[2026-03-31T14:43:53.053000+00:00] barnabasbusa: yeah I think it's good to have separate
```

</details>

