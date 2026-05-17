# Gloas attestation payload request specification and PR discussion

**Channel:** epbs | **Date:** 2026-02-18

## Summary

The discussion centers on a specification gap in Gloas attestation payload handling. Potuz identified that while the specification covers requesting payloads when receiving blocks that build on unseen payloads, it doesn't specify that clients should request payloads by root when receiving attestations for past blocks with index 1 where the payload is missing. This behavior should be documented in the appropriate specification guide.

Terencechain responded with PR #4939 to address this issue, but Potuz identified several problems that prevent it from merging as-is. The PR may require dependency PR #4918 to avoid forcing clients to ignore attestations they should process, and the P2P wording needs correction to use "IGNORE" with proper phrasing about payload visibility. Additionally, the PR has failing tests that need to be fixed.

The discussion concluded with Potuz noting he's on vacation and opened the PR temporarily to get feedback from Francesco. He indicated he would fix the failing tests upon return, suggesting the PR remains a work in progress pending resolution of the identified issues and dependencies.

## Participants

- jtraglia
- potuz
- terencechain

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-18T18:29:50.918000+00:00] potuz: Hello, before Gloas, when we receive an attestation for a block root we haven' t seen we request it. When we see a block that builds on a block we haven' t seen we request it. After Gloas, when we receive a block that builds on top of a payload we haven' t seen we request it. What I **haven' t seen specified** is that when we receive an attestation for a past block with index 1 and we do not have the payload, **we should request the payload by root** obtained from the bid from the block. This may actually be more costly than usual.
[2026-02-18T18:30:24.336000+00:00] potuz: For Prysm this is trivial though cause we track this on Forkchoice anyway
[2026-02-18T18:31:04.755000+00:00] potuz: But this should be specified IMO, I may have missed this and probably someone already took care of adding this sentence in the right validator.md or whatever guide it belongs
[2026-02-18T18:47:37.569000+00:00] terencechain: https://github.com/ethereum/consensus-specs/pull/4939
[2026-02-18T18:49:27.713000+00:00] jtraglia: Nice, looks good to me.
[2026-02-18T18:49:49.159000+00:00] jtraglia: Will merge after getting at least one other client to approve it.
[2026-02-18T18:49:49.267000+00:00] potuz: This cannot merge as-is
[2026-02-18T18:50:06.578000+00:00] jtraglia: Oh. What's the issue?
[2026-02-18T18:51:02.120000+00:00] jtraglia: FYI, <@363800010518822915> the title needs to be <= 68 chars.
[2026-02-18T18:51:40.923000+00:00] potuz: As phrased I think this requires https://github.com/ethereum/consensus-specs/pull/4918 Otherwise the client would be ignoring attestations that it is forced to process
[2026-02-18T18:52:18.862000+00:00] potuz: Also the wording is incorrect for P2P
[2026-02-18T18:52:42.014000+00:00] potuz: it should be IGNORE , the payload was seen. ... and then write up the rest.
[2026-02-18T18:53:48.462000+00:00] terencechain: I have ignore. i dont understand "the payload was seen. ... and then write up the rest." or what difference that makes if we put payload was seen first
[2026-02-18T19:01:06.930000+00:00] jtraglia: Are you still working on this PR? There are some failing tests.
[2026-02-18T19:01:37.600000+00:00] jtraglia: Also, FYI I just merged master into your branch, so do a `git pull` before fixing anything.
[2026-02-18T19:02:57.048000+00:00] potuz: Vacations, only working on whatever I broke in Prysm already 🙂
Also I opened that one as temporary to see what Francesco thinks. If that one will merge I can fix the tests when I get back
```

</details>
