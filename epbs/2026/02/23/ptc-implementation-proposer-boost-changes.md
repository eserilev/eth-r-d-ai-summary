# PTC Implementation and Proposer Boost Changes

**Channel:** epbs | **Date:** 2026-02-23

## Summary

Potuz raised two main implementation questions: whether clients are tracking PTC (Payload Timeliness Committee) data per node or just for the last two slots, expressing concern about adding 128 bytes per node to forkchoice while also worrying about synchronization bugs with minimal tracking. He also questioned the proposer boost implementation, specifically whether clients are computing `is_head_weak` on the parent slot as specified, which requires getting committees at that particular state rather than using the current approach of comparing to total committee weight of the justified state divided by slots.

Regarding devnet0 deployment, there was initial uncertainty about PTC inclusion, with clarification emerging that clients have implemented PTC gossip topics and can process messages, but are not currently producing PTC messages or including them in blocks, and PTC data is not being used in fork choice decisions yet.

Potuz noted that this partial implementation approach (gossip without block inclusion or fork choice integration) should be safe since it avoids potential downscoring issues while allowing testing of the networking components.

## Participants

- .paulharris
- nflaig
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-23T18:28:28.527000+00:00] potuz: Question to <@&1417820113981145228> are you guys tracking PTC to each node or just the last two slots (that's all that's needed). I'm unhappy adding extra 128 bytes per node to forkchoice. But also I don't want to risk synchronization bugs by just tracking the last two slots.
[2026-02-23T20:39:38.308000+00:00] potuz: Another one, <@504202741933932544> you said you implemented the changes for proposer boost, are you actually computing `is_head_weak` on the parent slot to the proposer boost block as specified? it requires to get the right committees at that particular state.
[2026-02-23T20:43:13.836000+00:00] potuz: Today we are comparing to the total committee weight of the justified state divided by the number of slots
[2026-02-23T20:44:41.041000+00:00] potuz: dealing with the equivocating indices from the committees on each head call seems to me a bug waiting to happen
[2026-02-23T20:53:37.187000+00:00] .paulharris: are we doing ptc for devnet0?
[2026-02-23T20:54:56.635000+00:00] potuz: Not sure what was agreed actually, I think people were able to process the attestations but weren't going to send them nor pack them
[2026-02-23T20:55:25.958000+00:00] .paulharris: i was told we werent but third hand information so i don't mind either way but currently we've not done it
[2026-02-23T22:28:03.375000+00:00] .paulharris: apparently we do send ptc messages and aggregate, just not looked at in fork choice
[2026-02-23T22:29:01.098000+00:00] nflaig: we have the gosssip topic implemented but we don't put ptc messages into block and neither do we produce them right now, processing should work though
[2026-02-23T22:30:36.112000+00:00] potuz: This should be perfect cause you can't get downscored with this combination
```

</details>
