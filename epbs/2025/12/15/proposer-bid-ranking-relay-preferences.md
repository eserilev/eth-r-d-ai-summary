# Proposer Preferences and Trust Lists

**Channel:** epbs | **Date:** 2025-12-15

## Summary

The discussion centered around how proposers can express preferences for trusted builders in Ethereum's proposer-builder separation. Terencechain proposed a `ProposerPreferences` container that would include a `trusted_builders` list broadcasted over gossip, but jtraglia raised concerns about privacy (publicly revealing trust relationships) and potential DoS vectors from large lists requiring validation of builder indices. The group quickly converged on using three existing endpoints instead of the gossip-based approach, which allows clients to reuse existing registration logic.

A secondary discussion emerged around signaling global acceptance of trusted payments over P2P networks. While jtraglia had initially included a `trusted_payments_accepted` boolean field in a proposer preferences PR, it was removed due to pushback. Terencechain suggested this could help P2P-only builders discover proposer preferences, but potuz clarified that P2P builders cannot have trusted payments as the network will not propagate them and has explicit rejection rules for such transactions.

The discussion concluded with agreement to proceed with the three-endpoint approach, and jtraglia requested review of PR #4792 in the consensus specs repository. The trusted payments flag remains an open question for potential future discussion at breakout sessions.

## Participants

- jtraglia
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-15T20:00:42.367000+00:00] terencechain: Catching up. Even over gossip, a proposer’s preference for trusted builders can be expressed with a new field like this:

```
class ProposerPreferences(Container):
    proposal_epoch: Epoch
    validator_index: ValidatorIndex
    fee_recipient: ExecutionAddress
    gas_limit: uint64
    trusted_builders: List[ValidatorIndex, ValidatorLimit]
```

Real question is whether a proposer wants to publicly reveal who they trust and whether that creates any downside. i think having a registration endpoint is good
[2025-12-15T20:06:01.925000+00:00] jtraglia: Yeah I don't think I would feel comfortable with this myself. It's sort of a DoS vector too, since you could encode a really big list. I suppose a gossip check could ensure that all of the builder indices are valid & active, but that feels like a lot of extra work and errorprone.
[2025-12-15T20:31:16.185000+00:00] potuz: Yeah, let's just keep the three endpoints, it's a rather trivial approach
[2025-12-15T20:31:27.338000+00:00] potuz: These three
[2025-12-15T20:32:39.439000+00:00] terencechain: that's good with me, client can reuse some logic with submit regstration today which is nice
[2025-12-15T20:46:55.053000+00:00] terencechain: <@592004585506340885> is there currently no way for a proposer to signal that it accepts trusted payments over p2p, even at a global level?

Have you considered adding a simple boolean flag to indicate that a proposer is globally ok with trusted payments, so builders without RPC and only want to use P2P can discover this preference?
[2025-12-15T20:58:38.800000+00:00] jtraglia: So in the proposer preferences PR, I did add a `trusted_payments_accepted: boolean` field, but quickly removed it after some pushback (https://discord.com/channels/595666850260713488/874767108809031740/1450134935737208994).
[2025-12-15T21:29:19.137000+00:00] terencechain: i dont feel strongly either, i just think it may be useful for p2p builder that want trusted payment. We can open this up to discuss at the break out. The good thing is this is easy to add or remove later on even later in the process so it doesnt have any surface area
[2025-12-15T22:34:29.038000+00:00] potuz: P2P builder cannot have trusted payments
[2025-12-15T22:34:43.320000+00:00] potuz: We will not propagate that on P2P of course
[2025-12-15T22:41:52.480000+00:00] terencechain: oh we even have a REJECT for that, excuse what i said then, it's no longer a valid proposition
[2025-12-15T22:55:53.370000+00:00] jtraglia: <@425572898787426305> Please review this PR I made: https://github.com/ethereum/consensus-specs/pull/4792
```

</details>
