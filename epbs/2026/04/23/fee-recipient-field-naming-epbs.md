# Fee Recipient Field Naming in ePBS

**Channel:** epbs | **Date:** 2026-04-23

## Summary

The discussion centers on potential naming confusion in the ePBS (enshrined Proposer-Builder Separation) specification regarding two different `fee_recipient` fields. m.kalinin points out that `bid.fee_recipient` specifies who receives the bid value, while `payload.fee_recipient` specifies who receives transaction fees - two fields with different semantic meanings but identical names. This could create confusion for developers not familiar with the Gloas specification details.

potuz acknowledges the naming issue but expresses reluctance to engage in naming discussions, citing concerns about tooling compatibility. They note that builders and relays have become accustomed to `fee_recipient` being the field where auction values are paid, though they ultimately remain neutral on whether either field should be renamed.

The discussion leaves the naming question unresolved, with m.kalinin suggesting a rename if still feasible, while potuz defers to others in the community to weigh in on the decision.

## Participants

- m.kalinin
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-23T06:32:45.886000+00:00] m.kalinin: Do i understand correcly that:
- `bid.fee_recipient` holds the recipient of the `bid.value`
- `payload.fee_recipient` holds the recipient of the transaction fees

The address in both of these fields may be the same or may not. From the protocol perspective (not the proposer/builder economics perspective) these two fields have different semantics but the same name -- I believe that would be confusing for anyone who isn't familiar with Gloas. I would suggest to change the name of `bid.fee_recipient` if this is still possible to be done. I don't fully get the tooling argument and maybe <@755590043632140352> can expand on this
[2026-04-23T11:39:46.307000+00:00] potuz: Nah, I'll let others chime in, I have allergy to naming discussions. By tooling I was thinking on the consumers of this that are likely builders and relays, which have gotten used to `fee_recipient`  be the field that they pay their auction value.
[2026-04-23T11:40:05.597000+00:00] potuz: I don't care if any of these fields is changed name to whatever
```

</details>
