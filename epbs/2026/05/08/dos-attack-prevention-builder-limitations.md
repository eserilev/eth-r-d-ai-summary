# DoS Attack Prevention and Builder Limitations

**Channel:** epbs | **Date:** 2026-05-08

## Summary

This discussion focuses on a DoS vulnerability affecting builders in the Ethereum ecosystem. Potuz references Pawel's explanation of why a particular approach isn't viable - attackers can easily DoS builders by submitting transactions that only reveal themselves as non-includable after the builder performs full simulation, which is a costly operation that requires no payment from the attacker.

Potuz also raises an unresolved technical issue regarding race condition handling, specifically questioning how to address racing scenarios without signature verification. This appears to reference a previous review that contains additional technical context not captured in these brief messages. The discussion leaves open the question of how to properly mitigate both the DoS vector and the race condition simultaneously.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T09:41:09.450000+00:00] potuz: Pawel explained why this is not possible: you can triviallly dos the builder because it only finds out that the tx is not includable after full simulation, and this doesn't cost any money
[2026-05-08T09:43:06.499000+00:00] potuz: Have you read my review? How do you deal with the race without the signature verification?
```

</details>
