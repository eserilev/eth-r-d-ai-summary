# Payload attestation and gossip validation conditions

**Channel:** epbs | **Date:** 2026-02-11

## Summary

This discussion centers on clarifying validation conditions for payload attestations and identifying a potential attack vector in the current gossip validation rules. The main issue identified is that a malicious builder could create two different payloads with the same committed block hash - one valid and one invalid - and distribute them strategically across the network. Since both payloads would pass gossip validation, PTC attesters would mark both as timely, but the gossip rule preventing nodes from accepting multiple payloads from the same builder for the same block root could prevent network convergence and cause unfair re-orgs of proposers who received the invalid payload.

Several mitigation strategies were discussed but each has drawbacks. Adding builder slashing operations could address the attack but might worsen the free option problem. Removing the duplicate payload gossip rule and having PTC attesters reject blocks when multiple payloads are detected would also exacerbate free option attacks. The most promising approach discussed was incorporating block hash calculation into consensus layer validation or replacing block hashes with SSZ roots in bids, though this faces challenges because builders cannot commit to the full execution envelope due to components like execution requests that may not be fully deterministic from the builder's perspective.

The discussion concludes with potuz suggesting that these attacks, while theoretically possible, are difficult to execute and ex-ante reorgs are no longer highly profitable, leaning toward leaving the current design unchanged. However, the conversation ends with ethdreamer questioning whether execution requests should indeed be deterministic when the builder controls the transactions, leaving this technical detail unresolved.

## Participants

- ethdreamer
- pawandhananjay
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-11T20:14:10.490000+00:00] ethdreamer: I have a question on the spec:
[2026-02-11T20:14:36.653000+00:00] ethdreamer: actually two spec clarifications
[2026-02-11T20:16:06.186000+00:00] ethdreamer: when constructing a payload attestation, the spec says:
```
If a previously seen SignedExecutionPayloadEnvelope references the block with root data.beacon_block_root, set data.payload_present to True; otherwise, set data.payload_present to False.
```
what is meant by *seen* in this sentence? What are the minimum validation conditions that must pass for a `ExecutionPayload` to be *seen*?
[2026-02-11T20:18:31.499000+00:00] ethdreamer: second question:
in the gossip validation conditions for an `execution_payload`, it says:
```
[IGNORE] The node has not seen another valid SignedExecutionPayloadEnvelope for this block root from this builder.
```
What is meant by *valid* here? Again what validation conditions must pass to be considered valid?
[2026-02-11T20:19:40.468000+00:00] potuz: First question, gossip validation, you need to verify the signature and the fact that the bid was committed.
[2026-02-11T20:20:17.266000+00:00] potuz: second question: exactly the same, gossip validation except this rule
[2026-02-11T20:22:06.568000+00:00] potuz: well, the double negation of the p2p rules makes the latest comment stupid, but I meant gossip validation, which makes the second statement recursive since you're asking what are the gossip validation rules 🙂
[2026-02-11T20:29:27.250000+00:00] ethdreamer: if the second question refers to gossip validations, then couldn't I attack the chain as a malicious builder after winning the auction by creating two payloads:

1. the valid payload that was committed to by the proposer
2. another invalid payload. It has different transactions, but I lie about the block hash and set the hash to the committed hash (so both payloads have the same committed hash)

If I time the release carefully, most of the network will get the valid payload. All PTC attesters will attest to the timeliness of the payload regardless of which one is received because both pass gossip validation.

If the next proposer got the *valid* payload, they will build on it. If their block has majority weight, the network will build on this chain. But the nodes that received the invalid payload will be prevented from importing the valid payload due to this gossip rule:
```
[IGNORE] The node has not seen another valid SignedExecutionPayloadEnvelope for this block root from this builder.
```
[2026-02-11T20:31:23.369000+00:00] potuz: they will request by RPC and import from the valid ones, but it's a valid concern
[2026-02-11T20:33:03.721000+00:00] potuz: There's a bigger problem the other way around I guess, the proposer gets the invalid one, then builds their block on top of empty, but the PTC and attesters got the valid one.
[2026-02-11T20:33:37.611000+00:00] ethdreamer: PTC votes the same way regardless of which payload they got
[2026-02-11T20:33:47.779000+00:00] ethdreamer: all will say it was timely
[2026-02-11T20:34:10.831000+00:00] potuz: correct
[2026-02-11T20:35:53.488000+00:00] ethdreamer: so in the case that the proposer got invalid, they get unfairly re-org'd?
[2026-02-11T20:36:08.577000+00:00] potuz: yeah, that's the biggest concern
[2026-02-11T20:36:33.773000+00:00] potuz: to cause an ex-ante because of this equivocation
[2026-02-11T20:37:45.432000+00:00] ethdreamer: Does this griefing of the proposer continue to propagate down the chain until a proposer that got the valid payload proposes?
[2026-02-11T20:38:09.168000+00:00] ethdreamer: assuming the attesters are weighted towards the valid payload
[2026-02-11T20:41:02.675000+00:00] potuz: yes
[2026-02-11T20:41:56.075000+00:00] ethdreamer: presumably this attack could be mitigated with a builder slashing operation in the future?
[2026-02-11T20:42:19.868000+00:00] potuz: doubt it works, slashing is only reasonable because of correlation
[2026-02-11T20:43:45.910000+00:00] ethdreamer: Not sure I understand what you mean by correlation. The attack is detectable and attributable without human intervention.
[2026-02-11T20:47:42.014000+00:00] ethdreamer: Theoretically the next proposer could submit a `BuilderSlashing` operation in their `BeaconBlock` which automatically gives them the right to not build on the previous payload. The previous proposer gets paid, what's left goes to the next proposer. All cost absorbed by the builder. No chain instability. What's wrong with that picture?
[2026-02-11T20:56:28.867000+00:00] ethdreamer: i suppose this could also by made much more difficult by having ptc attesters wait until the deadline to attest, and changing the meaning of a PTC attestation to mean that not only was a payload seen that passed the gossip validations, but **no equivocating payload** was seen as well
[2026-02-11T20:59:38.755000+00:00] potuz: Ah no that's fine, I thought you meant monetary slashing. Yeah we can allow the proposer to override in case of an equivocation, but probably that makes things like the free option problem even worse
[2026-02-11T20:59:46.983000+00:00] pawandhananjay: we could also make block_hash calculation part of the CL validation? Might be easier than all additional slashing conditions
[2026-02-11T21:00:28.890000+00:00] ethdreamer: i think this is simpler still
[2026-02-11T21:00:36.748000+00:00] potuz: This would be the easiest but that will take us longer
[2026-02-11T21:01:58.744000+00:00] ethdreamer: what if we simply removed this:
```
[IGNORE] The node has not seen another valid SignedExecutionPayloadEnvelope for this block root from this builder.
```
from the gossip validation conditions and imposed another rule on the PTC committee that you don't attest if more than one payload has passed the validation conditions
[2026-02-11T21:03:16.504000+00:00] ethdreamer: it would be trivial and fast to implement by manually hashing the payloads you see on gossip and ensuring that you never get a different hash
[2026-02-11T21:11:21.092000+00:00] ethdreamer: you need to still propagate both payloads, and the existence of more than one needs to invalidate both essentially..
[2026-02-11T21:12:21.638000+00:00] ethdreamer: ohhhhhh..... free option
[2026-02-11T21:14:04.195000+00:00] ethdreamer: without a slashing.. this makes the free option problem more economically attractive
[2026-02-11T21:20:37.870000+00:00] potuz: This
[2026-02-11T21:24:35.317000+00:00] potuz: We could add ssz root to the bid  to avoid rlp but even then we're subject to equivocations.
[2026-02-11T21:24:59.295000+00:00] potuz: The builder can also invalidate the payload on the CL with the same Blockhash
[2026-02-11T21:25:35.739000+00:00] potuz: So checking the hash is not enough
[2026-02-11T21:29:33.288000+00:00] ethdreamer: > We could add ssz root to the bid  to avoid rlp but even then we're subject to equivocations.

how would we still be subject to equivocations if checking the ssz root is part of the gossip validation rules? We would reject the invalid block
[2026-02-11T21:38:12.679000+00:00] ethdreamer: There is either zero or exactly one payload that hashes to the committed ssz root. That payload is either valid or it is not.. but either way, the whole network will agree. So to me it looks like problem solved..
[2026-02-11T21:48:31.404000+00:00] ethdreamer: what if we simply *replaced* the block hash with an ssz root in the bid?
[2026-02-11T21:49:29.352000+00:00] ethdreamer: at that point, we wouldn't even need the payload to be signed by the builder
[2026-02-11T21:51:41.918000+00:00] potuz: The ssz of the block. You can't commit to the full envelope
[2026-02-11T21:51:56.074000+00:00] pawandhananjay: what about the execution_requests
[2026-02-11T21:52:06.477000+00:00] ethdreamer: ah gotchu
[2026-02-11T21:52:11.003000+00:00] potuz: And more
[2026-02-11T21:52:11.219000+00:00] ethdreamer: okay so ssz root of the block instead of block hash
[2026-02-11T21:52:16.467000+00:00] ethdreamer: still requires builder signature
[2026-02-11T21:52:22.106000+00:00] potuz: Yes but even the block isn't enough
[2026-02-11T21:53:13.357000+00:00] ethdreamer: ah.. because you could mutate those other pieces..
[2026-02-11T21:53:35.727000+00:00] potuz: Yes, it's always impossible to commit to the full envelope
[2026-02-11T21:54:46.083000+00:00] ethdreamer: wait.. why?
[2026-02-11T21:55:09.475000+00:00] ethdreamer: the execution requests aren't known by the builder?
[2026-02-11T21:55:21.984000+00:00] potuz: Thus we can't find an invariant. We had talked early on about these kinds of attacks (they are worse in slot auctions for example) and decided that they weren't much of a problem as they are hard to pull off and an ex ante reorg is not lucrative anymore
[2026-02-11T21:55:33.534000+00:00] potuz: So I lean to leave this issue
[2026-02-11T21:56:21.084000+00:00] ethdreamer: i don't understand this though
[2026-02-11T21:59:27.138000+00:00] ethdreamer: these should be deterministic when you control the transactions though?
```python
class ExecutionRequests(Container):
    # [New in Electra:EIP6110]
    deposits: List[DepositRequest, MAX_DEPOSIT_REQUESTS_PER_PAYLOAD]
    # [New in Electra:EIP7002:EIP7251]
    withdrawals: List[WithdrawalRequest, MAX_WITHDRAWAL_REQUESTS_PER_PAYLOAD]
    # [New in Electra:EIP7251]
    consolidations: List[ConsolidationRequest, MAX_CONSOLIDATION_REQUESTS_PER_PAYLOAD]
```
```

</details>
