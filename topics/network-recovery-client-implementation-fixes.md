# Network Recovery and Client Implementation Fixes

---

## 2026-03-23 (epbs)

The discussion centers around efforts to recover a testnet that appears to be experiencing issues, with barnabasbusa leading the recovery effort and seeking fixes for the EPBS (Enshrined Proposer-Builder Separation) branch. The team has identified problems with both withdrawals and checkpoint synchronization, with 0xunclebill confirming they have a fix for withdrawals but are currently unable to sync due to checkpoint sync issues.

Barnabasbusa offers to redistribute validator keys to restore network finalization, contingent on getting the Lighthouse (lh) client back online through checkpoint sync fixes. However, 0xunclebill indicates limited progress on checkpoint sync repairs, with hopes to advance the work within the week. As an alternative, barnabasbusa considers using Lodestar for checkpoint syncing, noting they have access to a 2-3 day old checkpoint that would require time to sync to head, which terencechain confirms should work with a fresh database.

The discussion leaves several action items open: integrating fixes into the EPBS branch, resolving checkpoint sync issues for Lighthouse, and determining the timeline for implementing the withdrawal fixes to make network recovery efforts worthwhile.

**Participants:** 0xunclebill, barnabasbusa, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-23T06:26:00.716000+00:00] barnabasbusa: <@363800010518822915> can we get this into the epbs branch ?
[2026-03-23T06:26:10.555000+00:00] barnabasbusa: gonna try to recover the network
[2026-03-23T12:39:05.071000+00:00] barnabasbusa: <@358120958726373381> do you also have a fix for the withdrawals?
[2026-03-23T12:39:20.258000+00:00] barnabasbusa: I'm happy to try to get the network back in working order if I know that we have client implementations that could work
[2026-03-23T12:39:28.607000+00:00] barnabasbusa: otherwise its just gonna be a waste of effort
[2026-03-23T14:00:29.332000+00:00] 0xunclebill: yeah we have a fix but we can’t sync rn
[2026-03-23T14:00:57.307000+00:00] barnabasbusa: ah right, how far out is that?
[2026-03-23T14:01:10.234000+00:00] barnabasbusa: I could move around some validator keys to get us back to finalization
[2026-03-23T14:01:21.331000+00:00] barnabasbusa: so if checkpoint sync could be fixed, then we could get lh back in the game
[2026-03-23T14:02:04.825000+00:00] 0xunclebill: We haven’t made much progress on checkpoint sync hopefully we can push it forward this week
[2026-03-23T20:16:25.213000+00:00] barnabasbusa: should i be able to checkpoint sync from lodestar?
[2026-03-23T20:16:40.569000+00:00] barnabasbusa: i know it’s a 2-3d old checkpoint so might take a while to get to head
[2026-03-23T20:16:55.524000+00:00] terencechain: its fine as long as it's a fresh DB
```

</details>

---

## 2026-05-08 (epbs)

The discussion begins with jtraglia highlighting a technical benefit of a queued approach where deposits are only made to builders after finalization, which would simplify sites like beaconchain. The conversation then shifts to coordinating a voice call to brainstorm additional ideas related to the topic.

The participants arrange to meet in Discord's voice lounge channel, with some logistical coordination around attendance. One participant (potuz) experiences a power outage but indicates they could potentially join via mobile for voice-only participation, though they mention they could join properly in a couple of hours if power is restored.

The discussion concludes with the group moving to a voice channel to continue their brainstorming session, suggesting the text conversation was preliminary to a more detailed technical discussion that would happen verbally.

**Participants:** barnabasbusa, jtraglia, m.kalinin, potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-08T13:11:42.864000+00:00] jtraglia: No more bets lol.
[2026-05-08T13:12:24.176000+00:00] jtraglia: But I'll buy you a beer after Glamsterdam regardless 🙂
[2026-05-08T13:17:28.730000+00:00] jtraglia: Another nice benefit of the queue'd approach is that deposits are only made to builders after they are finalized. This will greatly simplify sites like beaconchain.
[2026-05-08T13:19:56.244000+00:00] m.kalinin: this is me on Monday's ACDT 😄
[2026-05-08T13:20:22.869000+00:00] barnabasbusa: we can probably hop in a call now to brainstorm some more ideas
[2026-05-08T13:21:04.779000+00:00] jtraglia: Link?
[2026-05-08T13:21:21.265000+00:00] jtraglia: Maybe a voice chat thing here on discord?
[2026-05-08T13:21:39.171000+00:00] m.kalinin: we have <#814925424726900766>
[2026-05-08T13:22:31.010000+00:00] m.kalinin: <@755590043632140352> will you join?
[2026-05-08T13:22:55.150000+00:00] potuz: I'm out of electricity
[2026-05-08T13:23:05.585000+00:00] m.kalinin: why are you here?
[2026-05-08T13:23:08.661000+00:00] potuz: Can join in a couple of hours if they return it
[2026-05-08T13:23:22.097000+00:00] potuz: Cell but that won't work for a meeting
[2026-05-08T13:23:52.110000+00:00] barnabasbusa: for voice it should work fine
[2026-05-08T13:24:13.836000+00:00] potuz: Ok should I go to the lounge?
[2026-05-08T13:24:24.657000+00:00] barnabasbusa: ye
[2026-05-08T13:24:31.989000+00:00] potuz: Just a sec
[2026-05-08T13:24:32.445000+00:00] barnabasbusa: no need to show yourself in 4k lol
```

</details>

