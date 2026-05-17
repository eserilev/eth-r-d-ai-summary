# Rate Limiting Issues in Devnet Sync

---

## 2026-04-06 (epbs)

0xunclebill reported encountering rate limiting issues with Prysm during devnet synchronization. The problem stems from having only one or two nodes available in the devnet that can serve the required data, causing the rate limits to be hit quickly. As an immediate solution, they plan to implement tighter self-rate limiting to reduce the pressure on available nodes.

The situation is further complicated by the recent addition of a new "by range request" feature for envelopes, which increases the consumption of rate limit quotas compared to previous implementations. 0xunclebill suggested that relaxing rate limits specifically for small devnet environments would be beneficial for future development and testing scenarios.

**Participants:** 0xunclebill

<details>
<summary>Raw messages</summary>

```
[2026-04-06T07:33:35.141000+00:00] 0xunclebill: we get rate limited pretty quickly by prysm. issue is theres only really one or two nodes in the devnet that can serve us data... ill add some tighter self rate limiting rates for now
[2026-04-06T07:34:08.743000+00:00] 0xunclebill: would be nice to relax these rate limits for small devnets in the future i think

also we've added a new by range request (envelopes) so thats going to eat at rate limits quicker than before
```

</details>

