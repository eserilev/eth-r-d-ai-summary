# PayloadAttributes SSE and Engine FCU in ePBS

**Channel:** epbs | **Date:** 2026-03-09

## Summary

The discussion focused on when PayloadAttributes SSE (Server-Sent Events) events are triggered in ePBS (enshrined Proposer-Builder Separation) implementations. bharath.vedartham asked about the timing differences between pre-Gloas and Gloas implementations, specifically whether the SSE event is sent when a block is received (pre-Gloas) or when the execution payload is received (Gloas), and how PTC (Payload Timeliness Committee) votes affect canonicity.

potuz clarified that payload canonicity is not directly tied to PTC votes - when a payload is received and imported, it becomes head regardless. The SSE event is sent when `engine_forkChoiceUpdated` (FCU) is called. bharath.vedartham confirmed their understanding that FCU is sent when `process_execution_payload` successfully runs, which potuz verified as correct.

The discussion resolved the timing question, establishing that PayloadAttributes SSE events are triggered upon successful execution payload processing and FCU calls, independent of PTC voting mechanisms.

## Participants

- bharath.vedartham
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-09T16:13:10.740000+00:00] bharath.vedartham: In ePBS, when is the PayloadAttributes SSE event which builder use is sent? 
I believe pre-gloas, it is sent when we receive a block and engine_forkChoiceUpdated is called. 
With gloas, I would assume it is sent when the execution payload is received? I assume if an execution payload is received but it is not canonical because it did not receive enough PTC votes, then another SSE event with the parent block executiom hash would be sent somehow?
[2026-03-09T16:17:37.822000+00:00] potuz: the canonicity of the payload does not have much to do with the PTC vote. When a payload is received and is imported, it will become head.  At any rate the answer is yes, when we send FCU
[2026-03-09T16:20:43.984000+00:00] bharath.vedartham: got it and just to clear my head, FCU is sent when `process_execution_payload` successfully runs?
[2026-03-09T16:29:58.486000+00:00] potuz: yes
```

</details>
