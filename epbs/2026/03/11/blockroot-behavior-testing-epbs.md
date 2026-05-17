# BLOCKROOT behavior testing in ePBS

**Channel:** epbs | **Date:** 2026-03-11

## Summary

Potuz identified a testing opportunity for ePBS devnet 0 related to BLOCKROOT behavior in the execution layer. The specific issue involves determining whether BLOCKROOT returns the parent blockroot or the blockroot of the block that included the parent hash. Currently these two values are equal, but they will differ in Gloas (presumably a future upgrade or implementation).

While Potuz doesn't expect this change to break existing contracts, they suggest it's worth testing this behavior proactively. They tagged another team member as being well-suited to investigate this particular technical issue on the current devnet.

## Participants

- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-11T21:42:10.319000+00:00] potuz: out of Private communications with <@917367578966523934> I realized there's something we could be testing right now on ePBS devnet 0 and is related to the EL more than the CL. We should check the behavior of BLOCKROOT, if it's the parent blockroot or the blockroot of the block that included the parent hash, today these are equal. In Gloas they are not. I doubt this breaks any contract, but still good to check this behavior. <@808969530608451584> you're perfect for these kinds of things 😄
```

</details>
