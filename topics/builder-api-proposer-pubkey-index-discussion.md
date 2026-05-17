# Builder API proposer pubkey vs index discussion

---

## 2026-05-08 (epbs)

bharath.vedartham raised a question for client teams regarding a change in the Builder API specification. Currently, the `getHeader` endpoint uses `proposer_pubkey` in requests, but there's a proposed change in PR #138 to use `proposer_index` instead for getting bids in the gloas specification. They asked for client teams' opinions on using proposer pubkey versus proposer index in this context.

wemeetagain responded affirmatively on behalf of Lodestar, though the brief nature of the response leaves the specific preference unclear. The discussion appears to be seeking broader client team input on this API design decision, but no clear consensus or detailed technical reasoning was provided in these messages.

**Participants:** barnabasbusa, bharath.vedartham, wemeetagain

<details>
<summary>Raw messages</summary>

```
[2026-05-08T14:39:54.493000+00:00] bharath.vedartham: I have a question for client-teams: In the builder-api today when we send a getHeader we send in the `proposer_pubkey`  here: https://github.com/ethereum/builder-specs/blob/main/apis/builder/header.yaml#L29
In gloas, it is specced to pass in the `proposer_index` instead of the pubkey in the request to get the bid https://github.com/ethereum/builder-specs/pull/138/changes#diff-a186f4bbbe96e38c7699013ced6e58643ff9a03dddbc4fd7584c817920f5ef41R60. 
Do clients have an opinion on using proposer pubkey vs using proposer index in this situation?
[2026-05-08T15:16:54.579000+00:00] wemeetagain: yes for lodestar
[2026-05-08T15:17:49.939000+00:00] barnabasbusa: he's got power
```

</details>

