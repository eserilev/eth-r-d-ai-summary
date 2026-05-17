# Proposer Head Function Deprecation in Gloas

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers around deprecating the `get_proposer_head` function in the Gloas upgrade, with reference to PR #5174 that would remove this functionality. m.kalinin asks about the deprecation status and notes that removing `get_proposer_head` would eliminate honest reorgs of untimely beacon blocks, requiring the removal of related functions like `is_head_late`, `is_head_weak`, and `is_parent_strong`.

Initially there was confusion about which functions could be safely removed, with jtraglia suggesting `should_apply_proposer_boost` and `get_weight` might also be candidates for removal. However, they clarified that these functions are still needed by `get_head`, so only the specific functions mentioned by m.kalinin would be removed.

The discussion reveals different implementation approaches among client teams. While there appears to be support for removing `get_proposer_head` from the specification itself, potuz indicates their team will maintain the current implementation paths internally rather than relying on FCU (forkchoice updated), citing safety concerns about making such changes despite supporting the spec-level removal.

## Participants

- barnabasbusa
- jtraglia
- m.kalinin
- potuz
- wemeetagain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T14:54:02.965000+00:00] m.kalinin: a different topic: did we deprecate `get_proposer_head` in Gloas?
[2026-05-08T14:55:25.623000+00:00] jtraglia: It should/would be deprecated if this PR is merged:
* https://github.com/ethereum/consensus-specs/pull/5174
[2026-05-08T14:56:18.640000+00:00] jtraglia: Actually, we should just delete `get_proposer_head` in that PR.
[2026-05-08T14:56:25.664000+00:00] m.kalinin: so, there gonna be no honest reorgs of untimely beacon blocks, right?
[2026-05-08T14:57:45.169000+00:00] m.kalinin: I see mods to `is_head_late`, `is_head_weak`, `is_parent_strong` in Gloas. We need to remove them as well if this is the case
[2026-05-08T15:01:19.925000+00:00] jtraglia: And possibly other functions. Like `should_apply_proposer_boost` and `get_weight`.
[2026-05-08T15:03:35.099000+00:00] m.kalinin: is `get_proposer_head` mod the only reason for `should_apply_proposer_boost` to exist?
[2026-05-08T15:04:56.990000+00:00] jtraglia: Ah wait. No. This is used by `get_head` which isn't removed.
[2026-05-08T15:05:08.150000+00:00] jtraglia: So yeah, just the functions you mentioned. Sorry about that.
[2026-05-08T15:16:54.579000+00:00] wemeetagain: yes for lodestar
[2026-05-08T15:17:49.939000+00:00] barnabasbusa: he's got power
[2026-05-08T15:17:54.800000+00:00] potuz: We will keep the current paths
[2026-05-08T15:18:20.747000+00:00] potuz: Rather than relying on fcu
[2026-05-08T16:26:47.101000+00:00] m.kalinin: what do you mean? you will keep `get_proposer_head` ?
[2026-05-08T16:27:56.481000+00:00] potuz: yes, it is more dangerous to change this
[2026-05-08T16:28:05.465000+00:00] potuz: there's no reason not to keep the exact same implementation
[2026-05-08T16:28:13.297000+00:00] potuz: of course I support getting rid of it from the spec
[2026-05-08T16:28:24.713000+00:00] potuz: and just rely on send_fcu to a previous head
```

</details>
