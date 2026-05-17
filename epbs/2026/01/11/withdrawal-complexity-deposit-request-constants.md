# Withdrawal Complexity and Deposit Request Constants

**Channel:** epbs | **Date:** 2026-01-11

## Summary

Potuz expressed concerns about proposed alternatives for handling deposit request constants, specifically objecting to complex solutions involving separate queues due to the already complicated nature of withdrawal paths. They also opposed completely removing the constant, citing concerns about a potential attack where blocks could be cheaply clogged with deposit requests.

Instead, Potuz suggested a compromise approach of lowering the current constant to a smaller but still meaningful value that would prevent the clogging attack while addressing other concerns. No consensus or specific decisions were reached in this brief comment, and the exact value for a reduced constant remains an open question.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-11T14:30:22.856000+00:00] potuz: Just to leave it here but I don't like the complex alternatives being discussed there like having separate queues and the such. Withdrawals are already a complicated path. I also don't like removing this constant altogether because of what I mentioned above of constantly clugging very cheaply all blocks with deposit requests. I agree that perhaps this constant can be lowered to something much smaller while still preventing this attack but it should be something meaningful I think
```

</details>
