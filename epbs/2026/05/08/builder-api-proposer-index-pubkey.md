# Builder API Proposer Index vs Pubkey

**Channel:** epbs | **Date:** 2026-05-08

## Summary

bharath.vedartham raised a question about the Builder API specification regarding whether to use `proposer_pubkey` or `proposer_index` when requesting block headers from builders. Currently, the builder-specs use `proposer_pubkey` in the getHeader request, but a pull request (#138) for gloas proposes changing this to use `proposer_index` instead when getting bids.

The question was directed at client teams to gather their opinions on which approach is preferable - using the proposer's public key versus using the proposer index for these API calls. No responses or consensus were provided in the discussion thread shown.

## Participants

- bharath.vedartham

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T14:39:54.493000+00:00] bharath.vedartham: I have a question for client-teams: In the builder-api today when we send a getHeader we send in the `proposer_pubkey`  here: https://github.com/ethereum/builder-specs/blob/main/apis/builder/header.yaml#L29
In gloas, it is specced to pass in the `proposer_index` instead of the pubkey in the request to get the bid https://github.com/ethereum/builder-specs/pull/138/changes#diff-a186f4bbbe96e38c7699013ced6e58643ff9a03dddbc4fd7584c817920f5ef41R60. 
Do clients have an opinion on using proposer pubkey vs using proposer index in this situation?
```

</details>
