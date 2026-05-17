# Finalization and Validator Setup Discussion

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around a validator setup where all validators are currently running on a single pod, with the expectation that this configuration should enable finalization. Potuz notes that achieving finalization in this setup will likely expose some bugs in the system, suggesting this is part of a testing or debugging process.

There's a plan to potentially move two additional pods to join the current setup if finalization is successfully achieved. The team confirms that Sam has the knowledge needed to perform this pod migration if the finalization test proves successful.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T02:33:33.110000+00:00] potuz: With these vals on only one pod we should finalize right?
[2026-03-05T02:33:42.055000+00:00] potuz: that will trigger some bugs probably too 🙂
[2026-03-05T02:34:06.206000+00:00] barnabasbusa: yeah it should
[2026-03-05T02:36:11.053000+00:00] potuz: will Sam know how to move the other 2 pods to this if it finalizes?
[2026-03-05T02:36:20.790000+00:00] barnabasbusa: yes
```

</details>
