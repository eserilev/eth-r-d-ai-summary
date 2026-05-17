# Post-Gloas checkpoint sync state expectations discussion

**Channel:** epbs | **Date:** 2026-04-01

## Summary

The discussion centers around state expectations for checkpoint sync servers after the Gloas upgrade. pawandhananjay raised confusion about whether checkpoint sync servers should return a pending state or could also return a full state when the finalized slot is a skip slot.

barnabasbusa directed the discussion to previous messages for context, while potuz provided clarification that the state should always be "post CL block." When pawandhananjay sought confirmation that this means the finalized state returned from checkpoint servers would always be the pending state (relative to the finalized block), potuz confirmed this understanding is correct.

The consensus reached is that post-Gloas, checkpoint sync servers should consistently return the pending state relative to the finalized block, regardless of whether the finalized slot is a skip slot or not.

## Participants

- barnabasbusa
- pawandhananjay
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-01T20:45:43.828000+00:00] pawandhananjay: i'm confused about which state to expect from the checkpoint sync server post gloas.  Should we always expect a pending state or can we also get a full state when the finalized slot is a skip slot.
[2026-04-01T21:57:02.359000+00:00] barnabasbusa: <@491624610215886849> welcome to the party. https://discord.com/channels/595666850260713488/874767108809031740/1479432783792439388 please read from here 😄
[2026-04-01T21:57:28.282000+00:00] barnabasbusa: TLDR: https://discord.com/channels/595666850260713488/874767108809031740/1479434870391312404
[2026-04-01T22:55:17.318000+00:00] potuz: it should be always post CL block
[2026-04-01T23:02:53.921000+00:00] pawandhananjay: the block here is the finalized block? so the finalized state returned from the checkpoint server would always be the pending state?
[2026-04-01T23:10:16.904000+00:00] potuz: yes
```

</details>
