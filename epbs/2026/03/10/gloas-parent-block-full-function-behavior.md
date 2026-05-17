# Gloas Fork is_parent_block_full Function Behavior

**Channel:** epbs | **Date:** 2026-03-10

## Summary

The discussion centers on the behavior of the `is_parent_block_full` function during the Gloas fork transition. Terencechain identified that when called on the beacon state at the first Gloas slot, the function returns true even though the last Fulu block had no full payload. This occurs because `UpgradeToGloas` initializes both `latest_execution_payload_bid.block_hash` and `latest_block_hash` to the same value (the last Fulu EL hash), causing the equality check to be satisfied spuriously.

The team confirmed this behavior is intentional - potuz explained that the function was designed to return true at the fork. Multiple developers (nc1234 and hopinheimer) indicated their internal caching systems treat all pre-Gloas blocks as FULL variants anyway, so this behavior doesn't break their implementations. However, potuz noted their team uses a hybrid approach where pre-Gloas blocks are still treated as empty in some contexts (using blockroots to access pre-Gloas poststates) but naturally treated as full in others.

While there's a potential option to move entirely to treating pre-Gloas blocks as full, potuz indicated this would be a more dangerous change to merge in the current development cycle, suggesting this remains an open consideration for future implementation.

## Participants

- hopinheimer
- nc1234
- pawandhananjay
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-10T23:43:43.974000+00:00] terencechain: Hi <@&1417820113981145228> has anyone run into the following scenario?  When calling `is_parent_block_full` on the beacon state at the first Gloas slot, the function returns true even though the last Fulu block had no full payload. This happens because `UpgradeToGloas` initializes both `latest_execution_payload_bid.block_hash` and `latest_block_hash` to the same (the last Fulu EL hash), so the equality check in is_parent_block_full is satisfied spuriously.  Does this  break anything for your internal caching
[2026-03-10T23:44:20.565000+00:00] potuz: We designed that function so that it returns true at the fork
[2026-03-10T23:46:23.061000+00:00] pawandhananjay: <@737658693331714159> ran into this issue when working on fork choice i think
[2026-03-10T23:47:19.491000+00:00] nc1234: Yes `is_parent_block_full` should be true at first gloas slot. Our internal caching treats all pre-gloas blocks to be FULL variants anyways
[2026-03-10T23:49:05.527000+00:00] hopinheimer: Yup I’m assuming all pre-Gloas blocks to be FULL as well. Since we have variants for ProtoNode.
[2026-03-10T23:49:30.288000+00:00] potuz: we have some sort of a hybrid because we didn't want to change the fact that we use blockroots to access pre Gloas poststates
[2026-03-10T23:49:41.629000+00:00] potuz: so we still treat them as empty
[2026-03-10T23:49:49.924000+00:00] potuz: but in some places they are naturally treated as full
[2026-03-10T23:50:31.679000+00:00] potuz: We could just move to treat them entirely as full, but that seemed like a more dangerous change to merge now in develop
```

</details>
