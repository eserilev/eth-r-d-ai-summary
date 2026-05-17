# Gloas DA Implementation and Implementer Coordination

**Channel:** epbs | **Date:** 2026-01-29

## Summary

Potuz is seeking coordination among Gloas implementers regarding a consensus specs pull request (#4875) that he wants to propose for inclusion in the All Core Devs Testing (ACDT) meeting on Monday. The key question is whether any teams have already begun implementing Data Availability (DA) checks using the current frozen specification for devnet zero, or if there's still time to adopt the alternative approach outlined in his PR before implementation begins.

Potuz indicates that Prysm is ready to move forward with DA implementation but would delay if other implementers prefer the alternative route proposed in PR #4875. He emphasizes that this decision needs to be made collectively before teams start implementing, as other pending PRs are relatively trivial changes that don't require hard forks and won't significantly impact implementation efforts.

The discussion includes some coordination issues around Discord tags for Gloas implementers, with requests for tag access, but the core technical question about DA implementation approach remains unresolved and appears to be awaiting broader implementer input.

## Participants

- .paulharris
- 0xunclebill
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-29T18:30:53.693000+00:00] potuz: is there a tag for gloas implementers?
[2026-01-29T18:31:21.638000+00:00] potuz: I want to get a temperature check on https://github.com/ethereum/consensus-specs/pull/4875 to see if it's added to ACDT on Monday
[2026-01-29T18:31:54.229000+00:00] potuz: I see Prysm is already moving to implement DA but if people prefer that route above I'd delay implementation in Prysm
[2026-01-29T18:38:53.429000+00:00] potuz: The question is if anyone has already started implementing DA with the current scheme, if so then we just stick to current frozen spec and continue as we are for devnet zero. But if no one has gotten to implement DA checks, then we may as well all decide on this before implementing.
[2026-01-29T18:39:46.105000+00:00] potuz: All the other ones PRs out there are rather trivial changes on implementation or arent hard forks so I don't mind
[2026-01-29T18:41:16.478000+00:00] jtraglia: <@&1417820113981145228>
[2026-01-29T18:49:33.947000+00:00] potuz: Crap I got to @ + g + l + a and autocomplete would not offer it
[2026-01-29T19:23:43.072000+00:00] .paulharris: can i get added to taht tag? 🙂
[2026-01-29T19:24:27.629000+00:00] jtraglia: cc <@199561711278227457>
[2026-01-29T20:36:19.694000+00:00] 0xunclebill: The question is if anyone has already
```

</details>
