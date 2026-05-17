# Optional execution proofs integration with consensus clients

**Channel:** epbs | **Date:** 2026-01-04

## Summary

taulepton_ is leading an effort to integrate optional execution proofs into consensus clients and has identified a key technical challenge around validator attestation behavior when execution proofs are not delivered in a timely manner. They have documented the details of this problem and potential solutions in a HackMD note and are seeking feedback from the community.

kevaundray responded by mentioning "Optional proofs with epbs" (likely referring to enshrined proposer-builder separation), suggesting there may be interactions or considerations between the optional execution proofs proposal and EPBS that need to be explored further. The discussion appears to be in early stages with the main deliverable being the technical specification document that outlines the open questions around attestation timing requirements.

## Participants

- kevaundray
- taulepton_

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-04T12:52:08.175000+00:00] taulepton_: Hi guys, I'm supporting the effort to integrate optional execution proofs into consensus clients. There is an open question around how/if a validator should attest if execution proofs are not timely.  I've written the details in the following note - https://hackmd.io/@frisitano/BJ9P2IpQ-e. Any feedback is appreciated.
[2026-01-04T13:34:58.675000+00:00] kevaundray: Optional proofs with epbs
```

</details>
