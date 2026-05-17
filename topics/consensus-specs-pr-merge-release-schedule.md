# Consensus specs PR merge and release schedule

---

## 2026-03-25 (epbs)

The discussion centers around the timeline for merging consensus specs PR #4979 and scheduling the subsequent release. Terencechain inquired about when the PR would be merged and released, to which jtraglia responded that it depends on developer approvals, with an expected release window of Thursday or Friday.

As of the discussion time, the PR had zero approvals, indicating it was still awaiting review from the development team. Jtraglia tagged what appears to be a developer role to draw attention to the pending PR that needs approval before the planned release can proceed.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-25T13:46:08.775000+00:00] terencechain: wen merge https://github.com/ethereum/consensus-specs/pull/4979 and release?
[2026-03-25T15:08:16.487000+00:00] jtraglia: Whenever devs approve it 😅
[2026-03-25T15:08:33.552000+00:00] jtraglia: Expect an release on Thursday or Friday.
[2026-03-25T15:11:22.396000+00:00] jtraglia: It currently has zero approvals.
[2026-03-25T15:13:19.707000+00:00] jtraglia: ^ <@&1417820113981145228>
```

</details>

---

## 2026-04-15 (epbs)

The discussion focused on planning for the alpha.5 release, with jtraglia seeking feedback on PR #5113 and asking clients about potential implementation difficulty. Potuz indicated the change would be trivial for their client but noted that one developer is handling most of the implementation alone.

Two main PRs were identified for inclusion in alpha.5: PR #5094 and the "gloas genesis" PR #5067. Barnabasbusa confirmed that PR #5067 should be included but noted it might need a rebase depending on when PR #5094 gets merged. Jtraglia acknowledged this dependency and committed to handling it.

The discussion ended with jtraglia asking for general sentiment about PR #5094, leaving this as an open question for the team to address before finalizing the alpha.5 release plan.

**Participants:** barnabasbusa, jtraglia, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-15T16:04:21.142000+00:00] jtraglia: How do clients feel about this change (https://github.com/ethereum/consensus-specs/pull/5113). Seems reasonable to me, but might be an annoying change for clients at this point in time. If you don't think it will be too bad, we can merge it. cc <@881905303011086387>
[2026-04-15T16:28:51.480000+00:00] jtraglia: <@&1417820113981145228> other than 5094, is there anything we want to see in the alpha.5 release?
[2026-04-15T16:32:40.707000+00:00] potuz: should be trivial for us but cc <@363800010518822915> who's implementing most alone.
[2026-04-15T16:35:36.811000+00:00] barnabasbusa: yes the gloas genesis pr
[2026-04-15T16:37:14.741000+00:00] jtraglia: This one? https://github.com/ethereum/consensus-specs/pull/5067
[2026-04-15T16:39:13.964000+00:00] barnabasbusa: yes
[2026-04-15T16:40:50.836000+00:00] barnabasbusa: might need a rebase if 5094 gets merged
[2026-04-15T16:42:07.398000+00:00] jtraglia: Definitely. Okay we'll deal with this too.
[2026-04-15T16:42:32.905000+00:00] jtraglia: How are we feeling about 5094?
```

</details>

---

## 2026-04-16 (epbs)

jtraglia requested reviews on two consensus spec PRs (#5117 and #4840) related to BAL and slot precompile functionality. During review, terencechain identified an inconsistency where the execution APIs include both BAL and slot number fields, but PR #5117 only addressed BAL without the corresponding slot precompile changes. This raised the question of whether both BAL and slot precompile should be included or just BAL alone.

The team reached consensus that both BAL and slot precompile should be implemented, with qu0b, barnabasbusa, and tersec supporting this approach. Barnabasbusa noted that the slot precompile has been part of BAL's devnet for a long time and shouldn't be removed now. jtraglia acknowledged missing the slot number requirement and indicated he would wait to make the necessary ExecutionPayload changes after PR #5117 is merged, since the EIP may not have included these changes when the PR was originally created in January.

The discussion concluded with terencechain emphasizing the need for quick updates to get a release and spec tests ready, as client implementation and review would become the bottleneck. Additionally, jtraglia mentioned another PR (#5088) requiring review, expressing preference for removing impossible condition checks that could be added back later if needed.

**Participants:** barnabasbusa, jtraglia, qu0b, terencechain, tersec

<details>
<summary>Raw messages</summary>

```
[2026-04-16T16:22:17.280000+00:00] jtraglia: <@&1417820113981145228> could I get some reviews on these two PRs:
* https://github.com/ethereum/consensus-specs/pull/5117
* https://github.com/ethereum/consensus-specs/pull/4840
[2026-04-16T16:45:53.394000+00:00] terencechain: is slot pre compile in? as i review: https://github.com/ethereum/execution-apis/blob/main/src/engine/amsterdam.md
there is `slotNumber: QUANTITY, 64 Bits`
so do we want to create another situation that diverge from engine api that only consits of BAL but not slot number?
[2026-04-16T16:52:39.348000+00:00] jtraglia: I don't fully understand the question. Isn't this required for interop?
[2026-04-16T16:53:55.658000+00:00] terencechain: yes, you did it in 4840, but dont we want it in 5117 too?
[2026-04-16T16:54:21.723000+00:00] terencechain: the payload needs to contain the slot number right?
[2026-04-16T16:55:49.214000+00:00] jtraglia: Hmm you're right. I missed that. Good catch. Will update.
[2026-04-16T16:57:25.355000+00:00] jtraglia: I'm not sure if there was an ExecutionPayload change in the EIP when I made this PR back in January
[2026-04-16T16:58:37.876000+00:00] jtraglia: It would be easier to make this change after 5117 is merged. I'm just going to wait.
[2026-04-16T17:01:55.890000+00:00] terencechain: the question is more, do we accept both BAL and slot pre compile or BAL only? 
i know for BAL yes but i didnt get a sense for slot pre compile during the call (probably i missed it)
i dont have a strong preference either
[2026-04-16T17:21:06.722000+00:00] jtraglia: cc <@412614104222531604>
[2026-04-16T17:23:06.445000+00:00] qu0b: i'd prefer to have both
[2026-04-16T17:55:37.008000+00:00] barnabasbusa: we should do both
[2026-04-16T17:55:54.030000+00:00] barnabasbusa: as it’s been part of bals devnet for a long time now
[2026-04-16T17:56:06.268000+00:00] barnabasbusa: we shouldn’t rip it out now
[2026-04-16T18:01:12.781000+00:00] jtraglia: Also devs, thoughts on this PR?
https://github.com/ethereum/consensus-specs/pull/5088
[2026-04-16T18:01:21.815000+00:00] jtraglia: Sorry, there's so much to review 😢
[2026-04-16T18:02:04.293000+00:00] jtraglia: My preference is that we shouldn't have impossible condition checks & if in the future we need this, we can always add it back.
[2026-04-16T18:02:12.589000+00:00] jtraglia: cc <@654267572107083777>
[2026-04-16T18:04:04.212000+00:00] terencechain: ok in that case <@755590043632140352> might want to update his PR or it will be a different PR. 
the sooner we can have a release / spec tests the better because bottleneck will be on the client implementation and review
[2026-04-16T18:05:51.902000+00:00] tersec: yeah I agree
```

</details>

---

## 2026-04-17 (epbs)

jtraglia requested reviews from the team on two consensus-specs pull requests. The first request was for PR #4840, followed a couple hours later by a request for additional reviews on PR #5067, with jtraglia apologizing for the repeated pings to the team.

No technical discussion of the PR contents occurred in these messages, and no reviews or feedback were provided in this channel. Both PRs remain as open action items requiring team review.

**Participants:** jtraglia

<details>
<summary>Raw messages</summary>

```
[2026-04-17T14:09:12.332000+00:00] jtraglia: <@&1417820113981145228> would appreciate reviews on this PR:
https://github.com/ethereum/consensus-specs/pull/4840
[2026-04-17T16:14:54.844000+00:00] jtraglia: <@&1417820113981145228> need more reviews on this PR:
https://github.com/ethereum/consensus-specs/pull/5067
Sorry to keep pinging everyone.
```

</details>

---

## 2026-04-27 (epbs)

barnabasbusa shared a link to Ethereum consensus specs pull request #5177 on GitHub. No additional discussion, technical details, decisions, or action items were provided in the channel at this time.

**Participants:** barnabasbusa

<details>
<summary>Raw messages</summary>

```
[2026-04-27T14:54:55.985000+00:00] barnabasbusa: https://github.com/ethereum/consensus-specs/pull/5177
```

</details>

