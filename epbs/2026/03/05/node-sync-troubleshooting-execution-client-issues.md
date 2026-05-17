# Node sync troubleshooting and execution client issues

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around a node synchronization issue where potuz is experiencing inconsistent sync behavior. Initially, the node was "going in circles" and not syncing properly, despite using a newer commit from Terence that worked fine in potuz's local environment. After checking the logs to verify the correct image was being used, potuz identified that the issue might be related to a corrupted data directory.

The troubleshooting involved wiping the database and restarting the execution client (Geth). Once restarted, Geth began snap syncing properly, and the node started making progress through the slots. However, potuz noted that the sync speed was significantly slower than expected, possibly due to the colocation environment, despite the connection having gigabit bandwidth.

By the end of the discussion, the node had progressed to slot 919, well past the point where it had previously been isolated due to downscoring peers. The immediate sync issue appears resolved through the database wipe, though the slower-than-expected sync speed in the colocation environment remains an open question.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T02:15:16.555000+00:00] potuz: it's going in circles,
[2026-03-05T02:15:20.972000+00:00] potuz: here it synced immediately
[2026-03-05T02:15:26.215000+00:00] potuz: are we sure it's picked the same image?
[2026-03-05T02:15:52.187000+00:00] barnabasbusa: you can see the commit in the start of the logs
[2026-03-05T02:17:02.444000+00:00] potuz: I don't recognize this one lol
[2026-03-05T02:17:13.476000+00:00] barnabasbusa: lol
[2026-03-05T02:17:23.410000+00:00] potuz: ahh is newer than my head
[2026-03-05T02:17:26.426000+00:00] potuz: it's Terence's
[2026-03-05T02:17:44.855000+00:00] potuz: yeah I don't know why it's not syncing perhaps I hadn't removed the data dir last time, will wipe it
[2026-03-05T02:20:22.866000+00:00] barnabasbusa: you sure his newer commit didn’t break something else?
[2026-03-05T02:20:31.497000+00:00] potuz: yeah I'm sure
[2026-03-05T02:20:41.966000+00:00] potuz: here it syncs to head immediately
[2026-03-05T02:20:47.503000+00:00] potuz: there's something weird
[2026-03-05T02:20:51.457000+00:00] barnabasbusa: then yeah wipe db and run
[2026-03-05T02:20:52.204000+00:00] potuz: how do I look at geth
[2026-03-05T02:21:03.103000+00:00] barnabasbusa: docker logs execution
[2026-03-05T02:21:32.845000+00:00] potuz: can I wipe geth as well
[2026-03-05T02:21:46.858000+00:00] potuz: and restart it with docker restart execution?
[2026-03-05T02:22:32.197000+00:00] potuz: ahh ok, geth says it's snapsyncing now, something is happening
[2026-03-05T02:23:13.486000+00:00] barnabasbusa: yeah
[2026-03-05T02:23:21.513000+00:00] barnabasbusa: that’s how you would wipe it
[2026-03-05T02:23:33.161000+00:00] barnabasbusa: but they need init stuff
[2026-03-05T02:23:46.153000+00:00] potuz: I can probably just remove the chaindata
[2026-03-05T02:23:53.806000+00:00] barnabasbusa: ok
[2026-03-05T02:24:20.623000+00:00] barnabasbusa: give it a sec if it’s syncing
[2026-03-05T02:24:42.422000+00:00] potuz: It's much slower than here at home, don't know why, perhaps colocation?
[2026-03-05T02:24:52.730000+00:00] potuz: anyway I'm up to slot 700 now
[2026-03-05T02:25:24.619000+00:00] potuz: that's way after we were isolated by downscoring everyone
[2026-03-05T02:27:03.291000+00:00] potuz: 919, slowly getting there
[2026-03-05T02:28:47.096000+00:00] barnabasbusa: it should have gigabit connection
```

</details>
