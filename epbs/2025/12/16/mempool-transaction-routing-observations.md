# Mempool transaction routing observations

**Channel:** epbs | **Date:** 2025-12-16

## Summary

The discussion centers on observations of mempool transaction routing behavior during what appears to be a network event. data.always noted that user transactions shifted back to or were rerouted through the public mempool, while searcher transactions did not exhibit the same behavior pattern. This suggests different routing strategies or responses between regular users and MEV searchers during the incident.

The participants concluded that this differential behavior was likely due to the unexpected nature and short duration of the event, combined with the timing occurring during nighttime hours when response times might be slower. The observation indicates that searchers may have different automated systems or strategies that prevented them from reverting to public mempool usage as quickly as regular user transactions did.

## Participants

- data.always
- l03443

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-16T15:45:54.230000+00:00] data.always: user txs shifted back (or were rerouted) to the public mempool, but searchers etc. don't seem to have. need to look a bit deeper.
[2025-12-16T21:16:06.024000+00:00] l03443: probably because it was an unexpected event and short lived?
[2025-12-16T21:20:10.671000+00:00] data.always: yes. and in the middle of the night.
```

</details>
