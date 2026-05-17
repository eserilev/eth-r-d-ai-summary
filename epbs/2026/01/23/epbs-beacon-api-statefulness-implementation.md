# ePBS Beacon API Statefulness and Implementation Issues

**Channel:** epbs | **Date:** 2026-01-23

## Summary

The discussion centers on a significant API design issue introduced by ePBS (enshrined Proposer-Builder Separation). With ePBS, the validator propose path now requires calling two separate endpoints - "get block" and "get payload envelope" - which must be called together. This creates statefulness in the beacon APIs because calling these endpoints on different beacon nodes would not work properly, breaking the current stateless design assumption.

The issue was previously raised in a GitHub PR discussion, and there's uncertainty about whether to resolve this statefulness problem or simply warn users about the limitation. The participants noted a similar case in Fusaka where they wanted to avoid passing blobs to the validator client only to have them passed back to the beacon node.

The next steps involve bringing this issue to the ACDT (All Core Devs Testing) meeting and getting feedback from validator client teams like Vouch and Vero. There's also a related beacon-APIs PR (#552) that's ready for review and would enable API spec testing once merged.

## Participants

- jameshe5018
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-23T16:20:01.569000+00:00] jameshe5018: Not sure if this is better for here, ACDT, or the <#710831784991916072>  channel
But with ePBS on the validator propose path, we now have to call 2 endpoints the get block and get payload envelope endpoints, because we have to call these together I believe that would make the beacon apis stateful because calling get block on one bn and calling get payload on another bn wouldn't work. we had a case in Fusaka where we wanted to not have to pass the blobs to the validator client only to pass it back to the beacon node.  
Do we try to do something to resolve this and make the beacon apis stateless again or do we warn users somehow for this?
[2026-01-23T16:23:35.807000+00:00] nflaig: I brought up the same here https://github.com/ethereum/beacon-APIs/pull/552#discussion_r2642624992, I think we should bring it up on ACDT and especially get feedback from Vouch/Vero on this. (maybe repost the same on api channel as well)
[2026-01-23T16:24:10.598000+00:00] jameshe5018: cool
[2026-01-23T16:48:43.507000+00:00] nflaig: also would be good to get more eyes on https://github.com/ethereum/beacon-APIs/pull/552, feel like it's in a pretty good state and merging it would allow to run api spec tests for us and iterate on spec on master branch
```

</details>
