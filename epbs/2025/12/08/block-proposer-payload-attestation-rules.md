# Payload Attestations blob_data_available Field Discussion

**Channel:** epbs | **Date:** 2025-12-08

## Summary

The discussion centered on the behavior of the `blob_data_available` field in payload attestations for PTC (Payload Timeliness Committee) validators. Tomi0x asked whether validators should set this field to `true` based solely on having their assigned custody columns available, rather than verifying complete blob availability. Potuz confirmed this is the expected behavior, leading bharath.vedartham to suggest clarifying this in the specification via PR #4769.

There was debate about the field naming, with tomi0x proposing either renaming it to `columns_data_available` or removing it entirely. However, potuz explained the field cannot be removed because non-super-node proposers need this information for the next block. jtraglia advocated keeping the `blob_data_available` name since blob data is the ultimate concern, though acknowledged the naming could be unintuitive given the actual implementation behavior.

The discussion concluded with bharath.vedartham offering to update the PR based on whatever naming convention the team agrees upon, leaving the specific naming decision as an open item while confirming the technical behavior is correct as currently implemented.

## Participants

- bharath.vedartham
- jtraglia
- potuz
- tomi0x

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-08T16:58:01.317000+00:00] tomi0x: For payload attestations, is it enough if PTC validators set `blob_data_available=true` if they have all their assigned custody columns for that block, without checking if all of the blobs are actually available?
[2025-12-08T17:24:09.533000+00:00] potuz: yes, that's the expected behavior
[2025-12-08T17:29:44.862000+00:00] bharath.vedartham: https://github.com/ethereum/consensus-specs/pull/4769 probably worth clarifying it in the spec then
[2025-12-08T17:34:54.357000+00:00] tomi0x: we were thinking it could either be renamed to `columns_data_available` or removed entirely.
[2025-12-08T17:56:37.819000+00:00] potuz: It can't be removed, the proposer for the next block needs to use this information if it's not a super node
[2025-12-08T18:19:11.917000+00:00] jtraglia: Also I would like to keep the `blob_data_available` name. The blob data is what's important.
[2025-12-08T18:20:08.371000+00:00] jtraglia: But I agree the naming is a bit unintuitive.
[2025-12-08T18:29:53.119000+00:00] bharath.vedartham: happy to update the PR to the name folks agree upon 🙏
```

</details>
