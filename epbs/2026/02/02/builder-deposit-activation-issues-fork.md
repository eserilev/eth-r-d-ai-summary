# Builder Deposit Requirements and Fork Transition Handling

**Channel:** epbs | **Date:** 2026-02-02

## Summary

This discussion centers on builder deposit requirements and timing during the transition from Fulu to Gloas fork. The conversation began when nero_eth identified a discrepancy in the specs regarding whether builder deposits must be "finalized" versus "processed" for activation. jtraglia acknowledged this was misleading language from when deposit finalization requirements were added, and submitted PR #4890 to clarify that builders are only active once their deposit epoch is finalized.

The technical discussion revealed important timing constraints for the fork transition. Builders wanting to be active at the Gloas fork boundary must submit deposits before `GLOAS_FORK_EPOCH-2` to ensure finalization, since `is_active_builder` requires `builder.deposit_epoch < state.finalized_checkpoint.epoch`. There's also a risk that deposits submitted too early could accidentally be processed as validator deposits instead of builder deposits, though this timing should be predictable days in advance based on the deposit queue status.

The team reached consensus on the clarified language in jtraglia's PR, with potuz noting that `upgrade_to_gloas` runs after epoch transition processing. An open consideration was raised about whether clients could ignore deposits with 0x03 withdrawal credentials before the Gloas fork to prevent accidental validator registration, but this would require a Fulu spec change.

## Participants

- bharath.vedartham
- jih2nn
- jtraglia
- nc1234
- nero_eth
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-02T07:47:28.597000+00:00] nero_eth: What is the latest around handling builders around the fork transition?
In the specs it seems a builder's deposit must be finalized although in builder.md it says *processed* here: ⁨`Builders are active as soon as the deposit is processed on the consensus layer.`⁩
[2026-02-02T11:13:05.728000+00:00] jih2nn: If you're referring to a transition from Fulu to Gloas, this [PR](https://github.com/ethereum/consensus-specs/pull/4868) would give you an answer
[2026-02-02T13:51:42.160000+00:00] jtraglia: Hmm yes, that sentence is a bit misleading now. We added the deposit finalization requirement & forgot to update that line. After ACDT, I'll submit a PR to get that cleared up.
[2026-02-02T14:14:53.941000+00:00] jtraglia: <@792404665068158998> does this make sense? https://github.com/ethereum/consensus-specs/pull/4890
[2026-02-02T14:31:47.939000+00:00] jtraglia: <@755590043632140352> <@845691100072509460> could I get a quick review on this?
[2026-02-02T14:33:54.741000+00:00] nero_eth: Yes it does, thanks for clarifying!
[2026-02-02T14:33:59.244000+00:00] bharath.vedartham: Just to be clear on it, We won't have active builders on Gloas fork and builders will be active only 2 epochs later post the first gloas finalized checkpoint? We will be self-building till then
[2026-02-02T14:35:54.845000+00:00] potuz: asked for a change, Bharath was confused by it so I think everyone will be
[2026-02-02T14:36:11.425000+00:00] potuz: I replied to you as well Barath, that statement is not strictly true
[2026-02-02T14:39:11.835000+00:00] jtraglia: Yup will further clarify. Good suggestion.
[2026-02-02T14:39:39.610000+00:00] bharath.vedartham: this is a nicer place to have a conversation haha, I am confused at the following:
If we look at `add_builder_to_registry`, the `deposit_epoch` is set to `compute_epoch_at_slot(slot)` which will be `GLOAS_FORK_EPOCH` at the hard fork boundary.
But `state.finalized_checkpoint` will be `GLOAS_FORK_EPOCH-2` right? because of the following check in `is_active_builder`:
```python
def is_active_builder(state: BeaconState, builder_index: BuilderIndex) -> bool:
    """
    Check if the builder at ``builder_index`` is active for the given ``state``.
    """
    builder = state.builders[builder_index]
    return (
        # Placement in builder list is finalized
        builder.deposit_epoch < state.finalized_checkpoint.epoch
        # Has not initiated exit
        and builder.withdrawable_epoch == FAR_FUTURE_EPOCH
    )
```
so `state.finalized_checkpoint.epoch`  will only be greater than the builder.deposit_epoch after 2 epochs right?
[2026-02-02T14:39:43.961000+00:00] bharath.vedartham: post the fork
[2026-02-02T14:39:48.414000+00:00] bharath.vedartham: assuming we have finality
[2026-02-02T14:39:58.355000+00:00] potuz: no
[2026-02-02T14:40:09.258000+00:00] potuz: the ⁨`slot`⁩ that is being passed is the slot of the deposit
[2026-02-02T14:40:15.072000+00:00] potuz: not of the state
[2026-02-02T14:42:01.642000+00:00] bharath.vedartham: ahh got it, so this will evaluate to the epoch at which they have created the deposit
[2026-02-02T14:42:48.711000+00:00] bharath.vedartham: but at the fork boundary, this has to be before the finalized checkpoint at `GLOAS_FORK_EPOCH`
[2026-02-02T14:43:11.332000+00:00] bharath.vedartham: so deposits will have to be done before  `GLOAS_FORK_EPOCH-2` if a builder wants to be active at the hard fork
[2026-02-02T14:45:45.132000+00:00] bharath.vedartham: just to clear my head, there is a chance that these deposits can become validators also right?
[2026-02-02T14:47:03.401000+00:00] nc1234: If the deposit is too early yeah, it would accidentally be processed as a validator deposit
[2026-02-02T14:48:04.289000+00:00] potuz: yes, this depends on the queue at the time of the fork, but it should be pretty damned obvious days in advance
[2026-02-02T14:48:09.007000+00:00] potuz: not even hours
[2026-02-02T14:50:03.303000+00:00] bharath.vedartham: Yeah I also assume there is no way for clients to have a pre-release before the hard fork where deposits with 0x03 withdraw credentials are not at all considered for the validator registry and they are only considered at the epoch boundary for gloas for builder activation
[2026-02-02T14:57:21.026000+00:00] bharath.vedartham: I mean to offset the risk of builders becoming validators by accident, clients could ignore validators with 0x03 withdraw credentials in `process_pending_deposit` before the gloas fork but that seems like that would require a spec change for Fulu
[2026-02-02T14:58:38.480000+00:00] jtraglia: <@1386598056320831509> <@755590043632140352> how does this sound?
[2026-02-02T14:58:40.018000+00:00] jtraglia: 
[2026-02-02T15:00:20.331000+00:00] bharath.vedartham: sounds good!
[2026-02-02T15:03:02.525000+00:00] potuz: Looks good to me, will let the bureaucrats argue if that should go on builder.md or in fork.md and then add a link to that.
[2026-02-02T15:03:10.998000+00:00] bharath.vedartham: will `upgrade_to_gloas` be run after processing justification and finalization for the hard fork epoch or is `upgrade_to_gloas` run before processing justification and finalization for the epoch? because I believe this will dictate which finalized epoch is considered for the builder to be active right?
[2026-02-02T15:03:51.611000+00:00] potuz: It's the last thing it's run, it's after epoch transition
```

</details>
