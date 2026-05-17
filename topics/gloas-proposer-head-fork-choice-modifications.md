# Gloas proposer head and fork choice modifications

---

## 2026-05-08 (epbs)

The discussion centers on whether to deprecate `get_proposer_head` in the Gloas upgrade, with initial consideration of removing it via PR #5174. m.kalinin questioned if this means there would be no more honest reorgs of untimely beacon blocks and identified several related functions that would also need removal: `is_head_late`, `is_head_weak`, and `is_parent_strong`. jtraglia initially suggested removing additional functions like `should_apply_proposer_boost` and `get_weight`, but clarified that these are still needed since `get_head` remains in use.

However, potuz ultimately decided to keep the current implementation paths rather than relying on FCU (forkchoiceUpdated), stating it's "more dangerous to change this" and there's no reason not to maintain the existing implementation. While supporting removal from the spec and relying on `send_fcu` to a previous head, the practical implementation will preserve `get_proposer_head`. m.kalinin noted this approach aligns with the payload reveal threshold, suggesting consensus on maintaining the current technical approach while potentially cleaning up the specification.

**Participants:** jtraglia, m.kalinin, potuz

<details>
<summary>Raw messages</summary>

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
[2026-05-08T15:17:54.800000+00:00] potuz: We will keep the current paths
[2026-05-08T15:18:20.747000+00:00] potuz: Rather than relying on fcu
[2026-05-08T16:26:47.101000+00:00] m.kalinin: what do you mean? you will keep `get_proposer_head` ?
[2026-05-08T16:27:56.481000+00:00] potuz: yes, it is more dangerous to change this
[2026-05-08T16:28:05.465000+00:00] potuz: there's no reason not to keep the exact same implementation
[2026-05-08T16:28:13.297000+00:00] potuz: of course I support getting rid of it from the spec
[2026-05-08T16:28:24.713000+00:00] potuz: and just rely on send_fcu to a previous head
[2026-05-08T16:29:08.164000+00:00] m.kalinin: yeah, it seems to be aligned with the payload reveal threshold
```

</details>

