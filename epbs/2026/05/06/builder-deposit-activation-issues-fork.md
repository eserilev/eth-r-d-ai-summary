# ePBS Builder Deposits and DOS Vector Concerns

**Channel:** epbs | **Date:** 2026-05-06

## Summary

This discussion raises concerns about a potential denial-of-service (DOS) vector in the upcoming ePBS (enshrined Proposer-Builder Separation) implementation. With planned gas limit increases to 100M+, the system would allow up to 8,192 builder deposits (totaling 193M gas) to be processed immediately in a single slot, bypassing the traditional deposit queue's rate-limiting mechanism. This creates a scenario where validators and builders might need to perform up to 8,192 signature verifications within a single slot, significantly more than the current system where signature verification is deferred and rate-limited.

The core question posed is whether all new builder deposits should bypass the queue or if only balance top-ups for existing builders should receive this expedited treatment, while new builder registrations remain rate-limited through the normal deposit queue. The trade-off identified is that rate-limiting new registrations would prevent builders from being available immediately at the fork boundary, potentially impacting the transition to ePBS.

No resolution or consensus was reached in this discussion, leaving the DOS vector concern and the optimal approach for handling builder deposits as open questions requiring further analysis.

## Participants

- pk910

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-06T11:37:18.899000+00:00] pk910: Moving over a conversation from the Interop Repricings channel:

With ePBS and planned gas limit increases to 100M+, we now allow up to 8192 builder deposits (193M gas) in a single slot to be processed immediately. Unlike the old deposit flow (where sig verification was deferred/rate-limited via the deposit queue), this means validators/builders may need to perform up to 8192 signature verifications in-slot in the worst case.

This sounds like a lot and might be a dos vector,  so the question is whether new builders should really bypass the queue, or whether only balance top-ups should bypass it while new builder registrations remain rate-limited through the normal deposit queue.
This would come with the downside of not having builders available at the gloas fork boundary.
```

</details>
