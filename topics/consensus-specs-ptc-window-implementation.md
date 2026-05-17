# Consensus Specs PTC Window Implementation

---

## 2026-03-23 (epbs)

The discussion centers around implementing a PTC (Payload Timeliness Committee) window in the Ethereum consensus specs, specifically regarding PR #4979. Potuz requested a review of the latest commit before proceeding with test fixes, while jtraglia made several changes including renaming `ptc_lookbehind` to `ptc_window`, refactoring `process_ptc_window`, and fixing tests.

A key debate emerged over whether to support the `MIN_SEED_LOOKAHEAD` parameter or hardcode the window to cover just three epochs (previous, current, next). Jtraglia initially preferred hardcoding to avoid issues when `MIN_SEED_LOOKAHEAD` is not 1, suggesting a fixed structure of `Vector[Vector[ValidatorIndex, PTC_SIZE], 3 * SLOTS_PER_EPOCH]`. However, potuz argued that maintaining `MIN_SEED_LOOKAHEAD` support is important for consistency across duty helpers and easier reasoning about proposals and PTC operations.

The discussion concluded with jtraglia reverting his hardcoding changes and restoring `MIN_SEED_LOOKAHEAD` support, following potuz's reasoning. While potuz mentioned that removing `MIN_SEED_LOOKAHEAD` entirely might be preferable, this was acknowledged as a separate issue for future consideration.

**Participants:** jtraglia, potuz

<details>
<summary>Raw messages</summary>

```
[2026-03-23T15:49:39.156000+00:00] potuz: <@592004585506340885> could you take a look at the last commit here? https://github.com/ethereum/consensus-specs/pull/4979 I don't want to start fixing tests if you'll change the core logic right away 🙂
[2026-03-23T17:53:18.830000+00:00] jtraglia: Do we want to support `MIN_SEED_LOOKAHEAD` future epochs? I'm thinking we should just hardcode this to 1. So prev, curr, next.
[2026-03-23T17:54:58.093000+00:00] jtraglia: I pushed a few changes. Fixed tests, renamed `ptc_lookbehind` to `ptc_window` (like <@363800010518822915> suggested), and refactored `process_ptc_window`.
[2026-03-23T17:55:42.524000+00:00] jtraglia: Before & now, if `MIN_SEED_LOOKAHEAD` is not 1, there will be issues.
[2026-03-23T17:59:27.562000+00:00] jtraglia: I would prefer we do:
```
ptc_window: Vector[Vector[ValidatorIndex, PTC_SIZE], 3 * SLOTS_PER_EPOCH]
```
[2026-03-23T18:43:26.104000+00:00] potuz: We have to support MIN_SEED_LOOKAHEAD IMO, there's no point of keeping it without supporting it across helpers for duties, it makes it easier to reason about proposals/ptc, etc. I also think we should remove MIN_SEED_LOOKAHEAD anyway and just hardcode everything to 1, but that's a different story
[2026-03-23T19:51:36.878000+00:00] jtraglia: Hmm let me think about this some more & maybe revert the changes I made.
[2026-03-23T20:31:18.728000+00:00] jtraglia: <@755590043632140352> thanks for your message. I've reverted my change. We're using `MIN_SEED_LOOKAHEAD` again.
```

</details>

---

## 2026-03-24 (epbs)

jtraglia requested reviews on two consensus specification pull requests related to PTC (Proof of Custody) window functionality. The first PR (#4979) implements changes that were agreed upon during the previous day's ACDT (All Core Devs Testing) meeting, indicating there was already consensus on the technical approach. A second PR (#5035) from another contributor was also flagged for review.

The discussion represents a straightforward coordination effort to gather the necessary reviews for moving forward with PTC window specifications that have already received preliminary agreement from the core development team.

**Participants:** jtraglia

<details>
<summary>Raw messages</summary>

```
[2026-03-24T13:24:55.460000+00:00] jtraglia: <@&1417820113981145228> can we get a few reviews on the PTC window PR which we agreed upon yesterday at ACDT? https://github.com/ethereum/consensus-specs/pull/4979
[2026-03-24T14:33:47.369000+00:00] jtraglia: Also, please review this PR from <@586161934425128960>:
https://github.com/ethereum/consensus-specs/pull/5035
```

</details>

---

## 2026-03-25 (epbs)

The discussion centers on implementing PTC (Payload Timeliness Committee) votes for devnet 1. Terencechain reported that Prysm has already been generating PTC votes for devnet 0, but proposers are not including them in blocks (this functionality is commented out). 

The key technical decision point is whether to add the PTC window to the state, which would be a consensus-breaking change that requires agreement across all clients. Potuz confirmed that adding the PTC window is the only additional change planned for devnet 1, with all other modifications being non-hardfork changes. The team needs to reach consensus on the state changes before proceeding with the devnet 1 implementation.

**Participants:** 0xunclebill, potuz, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-25T02:37:29.958000+00:00] 0xunclebill: Are we doing PTC votes for devnet 1?
[2026-03-25T03:05:39.281000+00:00] terencechain: prysm has been doing PTC votes for devnet 0 except proposer not packing them (commented it out)
we need to agree on whether to add the ptc window in state or not because that's consensus breaking
[2026-03-25T13:36:57.813000+00:00] potuz: yes, PTC window was the only extra change we want in devnet 1, the rest are non-hardforks
```

</details>

