# Ssz Limits Eip 7688

SSZ container limits and EIP-7688 implementation discussion

## Discussions
- [[epbs/2026/05/08/ssz-limits-eip-7688]]
---

## 2026-05-08 (epbs)

The discussion focused on SSZ limits and their resolution through EIP-7688. tersec pointed out that EIP-7688 already addresses SSZ limit concerns through its converted types specification. The EIP has been marked as "Considered for Inclusion" (CFI'd) for the Glamsterdam upgrade but hasn't been applied yet since it's not considered a headliner feature.

jtraglia raised concerns about an "AllBad" case that might require serious consideration. tersec concluded that if the core problem is increasing SSZ limits, this will be resolved naturally through the planned implementation of EIP-7688 in the upcoming upgrade cycle.

**Participants:** jtraglia, tersec

<details>
<summary>Raw messages</summary>

```
[2026-05-08T00:25:58.178000+00:00] tersec: as far as SSZ limits, EIP-7688 addresses this already: https://eips.ethereum.org/EIPS/eip-7688#converted-types
[2026-05-08T00:26:01.638000+00:00] jtraglia: So the `AllBad` case might need to be taken serious.
[2026-05-08T00:26:42.988000+00:00] tersec: And it's already CFI'd for Glamsterdam, it just hasn't been applied yet due to not being a headliner
[2026-05-08T00:27:00.803000+00:00] tersec: So if the problem to be solved is, increase the SSZ limits, that's going to happen naturally
```

</details>

