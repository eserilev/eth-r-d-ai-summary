# Proposer preferences and builder communication

**Channel:** epbs | **Date:** 2025-12-11

## Summary

The discussion centers on how validators will communicate their preferences (fee recipient, gas limit, etc.) to builders in Ethereum's proposer-builder separation architecture. Currently, this communication mechanism is under development through a new `proposer_preferences` gossip topic, as outlined in consensus specs pull request #4777 which is still under review.

There's interest in expanding the ProposerPreferences structure beyond basic parameters to include additional dynamic preferences that validators could pass to builders. One concrete suggestion mentioned is adding a minimum bid preference, which would allow validators to specify their minimum acceptable bid from builders.

The conversation leaves open the question of what other types of dynamic preferences might be valuable to include in this communication mechanism, suggesting this is an area of ongoing exploration in the Ethereum R&D community.

## Participants

- clemon1222
- jtraglia

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-11T22:28:45.639000+00:00] clemon1222: How will validators let builders know what their fee recipient/gas limit/etc be btw, are builders expected to support the registration endpoint?
[2025-12-11T22:32:11.021000+00:00] jtraglia: Via a new `proposer_preferences` gossip topic. Still under review 🙂 
https://github.com/ethereum/consensus-specs/pull/4777
[2025-12-11T22:37:51.505000+00:00] clemon1222: Oh nice! Perfect! Has there been any talks on expanding the ProposerPreferences to include some dynamic data in some way to pass along additional "preferences" to the builder?
[2025-12-11T22:39:11.618000+00:00] jtraglia: Just a little. I suggested adding a min-bid preference. What do you have in mind?
https://discord.com/channels/595666850260713488/1446368286823678045/1447687272215351517
```

</details>
