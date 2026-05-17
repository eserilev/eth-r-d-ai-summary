# Builder API Proposer Pubkey vs Index Discussion

---

## 2026-05-08 (epbs)

bharath.vedartham raised a question for client teams regarding a discrepancy in the builder API specification. Currently, the builder API uses `proposer_pubkey` when sending getHeader requests, but there's a proposed change in the gloas specification (PR #138) that would switch to using `proposer_index` instead of the public key in requests to get bids.

The question seeks input from client teams on their preference between using the proposer's public key versus the proposer index in this context. No responses or consensus have been provided yet in this discussion thread, leaving this as an open question awaiting client team feedback.

**Participants:** bharath.vedartham

<details>
<summary>Raw messages</summary>

```
[2026-05-08T14:39:54.493000+00:00] bharath.vedartham: I have a question for client-teams: In the builder-api today when we send a getHeader we send in the `proposer_pubkey`  here: https://github.com/ethereum/builder-specs/blob/main/apis/builder/header.yaml#L29
In gloas, it is specced to pass in the `proposer_index` instead of the pubkey in the request to get the bid https://github.com/ethereum/builder-specs/pull/138/changes#diff-a186f4bbbe96e38c7699013ced6e58643ff9a03dddbc4fd7584c817920f5ef41R60. 
Do clients have an opinion on using proposer pubkey vs using proposer index in this situation?
```

</details>

