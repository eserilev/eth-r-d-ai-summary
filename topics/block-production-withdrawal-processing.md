# Block Production and Withdrawal Processing Implementation

---

## 2026-04-14 (epbs)

The discussion centers on implementing block production in a new paradigm where proposers must execute `process_parent_execution_payload` before forming bids to determine correct withdrawals. The core challenge is that `get_expected_withdrawals(state)` must be called with the post-state after processing withdrawal requests, requiring proposers to process the parent execution payload first to update builder payment queues and partial withdrawals.

sproul proposed making `process_parent_execution_payload` optional in block processing to avoid running it twice - once during bid creation and again during block processing. The group initially considered caching the state after processing payloads, but potuz noted this would make caching complex and bug-prone. Instead, they concluded the computation is likely fast enough to run without caching, though this requires copying state which isn't entirely free.

The specification for `prepare_execution_payload` was identified as underspecified and was updated during the discussion by nflaig to consult `get_head` and remove the problematic `is_parent_block_full` function. The main remaining concern is performance impact from state copying and materializing changes in validators and balances from execution requests, which teams like Teku plan to monitor and evaluate.

**Participants:** bharath.vedartham, nflaig, potuz, sproul, tbenr, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-14T07:40:44.696000+00:00] sproul: Anyone else looking at block production in the new paradigm? Seems like we need to execute `process_parent_execution_payload` while proposing, before forming the bid. We need to process the parent payload to know what the withdrawals should be for the block we're proposing, right?
[2026-04-14T07:57:31.352000+00:00] bharath.vedartham: yeah i m trying to wrap my head around that too, `process_parent_execution_payload` adds the builders pending payment to the builder payment withdrawal queue and adds partial withdrawals too via `process_withdrawal_request`. I am not sure if using `get_expected_withdrawals(state)` in `prepare_execution_payload` is right?
[2026-04-14T07:59:59.901000+00:00] sproul: yeah I don't think it is, I think we need to make `process_parent_execution_payload` optional in block processing, and do something like:

- if deciding to build on parent block's bid: run `process_parent_execution_payload` on state
- compute withdrawals for EL interaction/bid creation
- process beacon block (now including bid we just built), but with `process_parent_execution_payload` _NOT RUNNING_ if we are building on parent block's bid (don't want to run twice)
- compute state root for beacon block -> inject -> sign, usual block building process
[2026-04-14T08:00:11.354000+00:00] sproul: I _think_. I'm a bit fried for today, but will revisit tomorrow.
[2026-04-14T09:13:38.725000+00:00] potuz: Yes this is correct, proposing was flagged as harder with this approach, I initially thought about caching the state after receiving the Payload and processing it (perhaps with an epoch transition in the middle) but dismissed it as it would make the cache very complex. Perhaps it makes sense to precompute this when we are proposing only
[2026-04-14T09:16:03.718000+00:00] nflaig: this is underspecified right now, need to update `prepare_execution_payload`, looking into how to make that minimal
[2026-04-14T12:35:05.074000+00:00] nflaig: thanks for looking into that, the spec was not fully updated for this, see `prepare_execution_payload` it's much simpler than what you describe
[2026-04-14T14:09:47.468000+00:00] terencechain: The issue is you can’t really use `is_parent_block_full` from beacon state helper. You need to compute to know whether, for a given parent root and hash, you are building on a full or empty parent.

But once you know whether you are building on full or empty, the rest seems pretty straightforward right? Nothing really changes there. 

If you are building on empty,  use `state.payload_expected_withdrawals`
If you are building on full, use`get_expected_withdrawals(state)`
[2026-04-14T14:10:09.337000+00:00] nflaig: `is_parent_block_full` no longer exists
[2026-04-14T14:10:36.916000+00:00] terencechain: ya good. i havne't checked latest spec.. i did remove most of `is_parent_block_full` from prysm code
[2026-04-14T14:11:45.889000+00:00] nflaig: you where referring to `prepare_execution_payload`?
[2026-04-14T14:12:03.528000+00:00] nflaig: I just updated that today, it should work now, we just consult `get_head`
[2026-04-14T14:12:16.967000+00:00] terencechain: ya. mainly how a proposer should pack withdrawals. Re: sproul's comment
[2026-04-14T14:29:28.494000+00:00] potuz: no this doesn't work, because you need to call `get_expected_withdrawals(state)` with the post-state of the requests.
[2026-04-14T14:31:56.724000+00:00] terencechain: wow you are right, i missed that. That's annoying
need to study how to make prysm work with this
[2026-04-14T14:36:04.189000+00:00] nflaig: did you check my latest updates to `prepare_execution_payload`?
[2026-04-14T14:41:23.402000+00:00] terencechain: i was a few commits behind, just checked `prepare_execution_payload` and it makes sense to me
[2026-04-14T14:47:27.647000+00:00] tbenr: <@755590043632140352> is this the caching you were talking about? so you probably want to apply envelop requests immediately to precompute the withdrawals if you are the next proposer so you'll have them already if you decide do build on full?
[2026-04-14T14:48:44.829000+00:00] potuz: Yes but if we do this our cache becomes actually much more complicated and bug prone as I was complaining early on. I think the computation is fast enough that probably we will be fine without caching that's why I stopped complaining
[2026-04-14T14:49:34.371000+00:00] tbenr: yes, it seems to me a fast mutation and we don't need to hash the state neither
[2026-04-14T14:49:46.813000+00:00] tbenr: we need to check on teku 😄
[2026-04-14T15:35:34.429000+00:00] terencechain: i think the main cost here is you need to copy the state and that's not "free"
[2026-04-14T15:42:09.219000+00:00] tbenr: copying isn't an issue for us, but materializing changes in validators and balances carried by execution requests might not be that cheap. We will track the performance and evaluate.
```

</details>

---

## 2026-05-02 (epbs)

tbenr reported triggering a build that includes several fixes aimed at improving block production stability. The changes are expected to address existing stability issues in the block production process, though the specific nature of the fixes or the underlying problems were not detailed in this brief update.

No further discussion, decisions, or follow-up actions were mentioned in this single message update.

**Participants:** tbenr

<details>
<summary>Raw messages</summary>

```
[2026-05-02T23:27:53.821000+00:00] tbenr: triggered a build with a couple of fixes, hopefully we will be more stable in block production
```

</details>

