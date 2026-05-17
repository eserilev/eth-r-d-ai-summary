# Fee Recipient Field Naming Discussion

---

## 2026-04-23 (epbs)

m.kalinin raised a concern about potential confusion in the fee recipient field naming convention in the Gloas protocol. They noted that `bid.fee_recipient` holds the recipient of the bid value while `payload.fee_recipient` holds the recipient of transaction fees - two fields with different semantic meanings but identical names. From a protocol perspective, this could be confusing for developers unfamiliar with Gloas, and they suggested renaming `bid.fee_recipient` if still possible.

potuz responded that they prefer to avoid naming discussions and explained that the "tooling argument" refers to existing consumers like builders and relays who are already accustomed to `fee_recipient` being the field where auction values are paid. However, they expressed indifference about changing either field name to resolve the confusion.

The discussion remains unresolved with no clear consensus on whether to proceed with renaming for clarity or maintain backward compatibility with existing tooling.

**Participants:** m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-23T06:32:45.886000+00:00] m.kalinin: Do i understand correcly that:
- `bid.fee_recipient` holds the recipient of the `bid.value`
- `payload.fee_recipient` holds the recipient of the transaction fees

The address in both of these fields may be the same or may not. From the protocol perspective (not the proposer/builder economics perspective) these two fields have different semantics but the same name -- I believe that would be confusing for anyone who isn't familiar with Gloas. I would suggest to change the name of `bid.fee_recipient` if this is still possible to be done. I don't fully get the tooling argument and maybe <@755590043632140352> can expand on this
[2026-04-23T11:39:46.307000+00:00] potuz: Nah, I'll let others chime in, I have allergy to naming discussions. By tooling I was thinking on the consumers of this that are likely builders and relays, which have gotten used to `fee_recipient`  be the field that they pay their auction value.
[2026-04-23T11:40:05.597000+00:00] potuz: I don't care if any of these fields is changed name to whatever
```

</details>

