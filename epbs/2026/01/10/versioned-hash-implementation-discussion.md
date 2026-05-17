# Versioned Hash Implementation Discussion

**Channel:** epbs | **Date:** 2026-01-10

## Summary

__kasey__ initially suggested replacing an unspecified component with a versioned hash (32 bytes), arguing it would be more useful than the current implementation. However, they immediately realized a complication: the implementation would require multiple versioned hashes (n versioned hashes) rather than a single one.

Upon recognizing this constraint, __kasey__ retracted their suggestion ("nm"), indicating the proposed versioned hash approach wouldn't work for the multi-hash requirement. The discussion ends without a resolution or alternative proposal being offered.

## Participants

- __kasey__

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-10T02:39:21.696000+00:00] __kasey__: My suggestion is to replace this with a versioned hash, which would also be 32 bytes, but is far more useful.
[2026-01-10T02:40:03.523000+00:00] __kasey__: Ah crap, but it’s n versioned hashes
[2026-01-10T02:40:15.481000+00:00] __kasey__: So… nm
```

</details>
