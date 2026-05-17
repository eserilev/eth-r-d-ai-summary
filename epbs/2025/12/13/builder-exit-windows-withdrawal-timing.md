# Builder identification and withdrawal structure changes

**Channel:** epbs | **Date:** 2025-12-13

## Summary

The discussion centers on how to handle builder identification in withdrawal structures when builders are not identified by validator index. Three main approaches emerged: (1) moving all withdrawal structures to use pubkeys instead of indices, (2) adding separate withdrawal queues/structures specifically for builders, and (3) using a "hack" approach with bit flags or arithmetic operations to distinguish builder indices from validator indices within existing structures.

The team expressed concerns about implementation complexity for each approach. Moving to pubkeys would require significant performance-impacting changes since pubkeys are currently only used for signature verification, while adding new structures would create substantial boilerplate code. The bit flag approach (using flags like `0x0100000000000000` for validators and `0x0200000000000000` for builders) gained the most support as a compromise, with jtraglia creating a working branch demonstrating this method without changing existing withdrawal structures.

Two pull requests were created to implement different options, with the bit flag approach (option 2) receiving the most positive feedback from terencechain. The discussion concluded with clarification that the `is_builder`/`is_validator` helpers would be simple arithmetic operations rather than expensive caches, though tersec noted potential implementation concerns around cache expenses that some clients already handle for validators.

## Participants

- jtraglia
- potuz
- terencechain
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-13T06:05:36.879000+00:00] tersec: presumably this is based on builders not being identified by validator index?
[2025-12-13T06:05:55.478000+00:00] tersec: i.e. it's worse in that scenario
[2025-12-13T09:14:32.194000+00:00] potuz: Yes but I believe this is one of those things that looks easier in the spec and it's massive in implementations which is the worse possible trade-off
[2025-12-13T09:14:54.017000+00:00] potuz: Moving to pubkey in these structures will be a nightmare
[2025-12-13T09:20:37+00:00] potuz: Adding new lists for builders is not that bad. A ha ck to deal only with indices could be to use maxuint - idx to signal builder idx.
[2025-12-13T15:20:19.571000+00:00] terencechain: The devil is in details. My concerns are: 
1. We have to add new withdrawals code that is 3 new types 
2. We have to touch existing caches. Most client have some form of index to pubkey cache or other way around
[2025-12-13T15:43:42.201000+00:00] jtraglia: What do we think about keeping index fields in withdrawal structures but setting a bit of the index to indicate which type of index it is. For example:
```python
VALIDATOR_INDEX_FLAG = 0x0100000000000000
BUILDER_INDEX_FLAG = 0x0200000000000000

def has_validator_index_flag(index: GenericIndex) -> bool:
    return (index & VALIDATOR_INDEX_FLAG) != 0

def has_builder_index_flag(index: GenericIndex) -> bool:
    return (index & BUILDER_INDEX_FLAG) != 0
```
This feels a little hacky but it might be a good compromise. I prefer short-term suffering (pubkeys) over long-term confusion though.
(apparently <@755590043632140352> suggested something like this somewhere but I cannot find it)
[2025-12-13T15:47:02.162000+00:00] jtraglia: I would still want to rename the fields though (`validator_index` -> `index`)
[2025-12-13T16:13:07.260000+00:00] potuz: Any change in the existing structures I think it's a big change in implementations TBH
[2025-12-13T16:13:19.795000+00:00] potuz: We can work this without them
[2025-12-13T16:14:21.517000+00:00] jtraglia: So in withdrawal structs, you want to use the `ValidatorIndex` type for builders & validators?
[2025-12-13T16:14:37.065000+00:00] jtraglia: I mean we can do this, but it's awfully confusing long-term.
[2025-12-13T16:14:56.731000+00:00] potuz: This. Or add new structures for builders
[2025-12-13T16:30:45.801000+00:00] jtraglia: I'll work on a branch with the "index hack". I do not support new structs just for builders.
[2025-12-13T16:30:53.617000+00:00] jtraglia: As a proof of concept. So we can compare.
[2025-12-13T17:14:57.262000+00:00] terencechain: what do we mean by adding new structures for builders. I thought we already are doing it regardless
[2025-12-13T17:17:44.101000+00:00] potuz: No, we'd need pending partial withdrawals and withdrawals/deposits for builder indices especially
[2025-12-13T17:19:19.435000+00:00] potuz: Anyway I take something back: although I believe it's a big drawback to have pubkeys inside helpers and it's going to surely affect performance. It's not as horrible of a rework as I thought cause the helpers can be backported to use pubkey on previous fork and won't affect sync.
[2025-12-13T17:21:05.703000+00:00] terencechain: Ya If that would be easier but not ideal long term so I will prefer to move everything to pubkey. And take the short term pain
[2025-12-13T17:21:50.693000+00:00] potuz: I won't. Having pubkeys in this helpers is still a very bad idea
[2025-12-13T17:37:57.189000+00:00] terencechain: neither is pretty, but i think the two options on the table are:
1. change all withdrawal related structures to pubkey
2. add new queues in state for builder
right? <@592004585506340885>
[2025-12-13T17:38:26.318000+00:00] potuz: there's a third one which is to hack the index
[2025-12-13T17:38:36.163000+00:00] potuz: and use maxuint-idx for builder index idx
[2025-12-13T17:38:38.504000+00:00] potuz: for example
[2025-12-13T17:42:53.089000+00:00] jtraglia: Yes, there's a 3rd option. Here's a branch with these changes. All tests are passing.
https://github.com/ethereum/consensus-specs/compare/builders-no-updated-withdrawal-types
[2025-12-13T17:47:19.071000+00:00] jtraglia: I'd prefer to just keep it simple & use a bit flag. See the branch.
[2025-12-13T17:47:51.024000+00:00] jtraglia: cc <@520034910149410861> too.
[2025-12-13T17:51:21.172000+00:00] potuz: changing the structure is bad eitherway, with the bit or the pubkey, we'd need to add new structures and deal with them pre/post-fork, it really blows this simple rewrite of the helper into thousands of lines of boilerplate
[2025-12-13T17:54:22.594000+00:00] jtraglia: Have you looked at the branch? It doesn't change the withdrawal structures.
[2025-12-13T17:56:01.924000+00:00] jtraglia: And boilerplate is annoying but not a huge deal, right? I'd like for us to make the best long-term decision.
[2025-12-13T17:57:25.243000+00:00] potuz: I agree, and I think that moving pubkeys into internals is a huge change for such a secondary feature. We currently only lookup pubkeys for signature verification.
[2025-12-13T18:01:01.517000+00:00] potuz: So the `is_builder_index()` approach is equivalent to my hack right?
[2025-12-13T18:02:40.297000+00:00] jtraglia: Yes.
[2025-12-13T18:03:39.294000+00:00] potuz: yeah that's an option
[2025-12-13T19:27:08.669000+00:00] terencechain: i like this one the most
will be good to open a PR so people can start leave comments
[2025-12-13T19:41:36.385000+00:00] jtraglia: So now there's the following PRs:
* [Make builders non-validating staked actors (option 1)](https://github.com/ethereum/consensus-specs/pull/4787)
* [Make builders non-validating staked actors (option 2)](https://github.com/ethereum/consensus-specs/pull/4788)
[2025-12-13T19:54:44.042000+00:00] tersec: will have to see how `is_builder` and `is_validator` are actually implementable
[2025-12-13T19:55:11.159000+00:00] tersec: `is_validator` is something some CLs have already basically
[2025-12-13T19:55:45.139000+00:00] jtraglia: Meaning there would be a naming conflict?
[2025-12-13T19:56:15.343000+00:00] jtraglia: To be honest, we could probably just remove those helpers.
[2025-12-13T19:56:21.427000+00:00] tersec: I mean, these caches can be expensive
[2025-12-13T19:56:55.668000+00:00] tersec: Nimbus does a fairly nuanced thing to make it not expensive (either RAM or CPU) for validators. it _should_ work for the builders one
[2025-12-13T20:50:07.841000+00:00] potuz: But these is_builder or is_validator is an arithmetic operation only, not a cache. The other global index->pubkey one we can just duplicate what we already do for validators.
```

</details>
