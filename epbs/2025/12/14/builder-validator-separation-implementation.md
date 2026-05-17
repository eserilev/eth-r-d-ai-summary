# Builder validator separation and implementation approach

**Channel:** epbs | **Date:** 2025-12-14

## Summary

The discussion centers on builder-validator separation implementation, where .paulharris initially wondered if it would be better to keep builders out of the validator list to avoid index churn and simplify reasoning about validator duties. However, jtraglia clarified that this approach is already what they're implementing.

The conversation then shifts to reviewing two PRs that jtraglia has written, both with passing tests. Potuz expresses preference for "Option 2" and notes it's impressive to get tests passing, indicating likely support for the changes barring implementation complexity issues. The second PR was reportedly easier to implement, involving copying everything, making spec changes, and reverting tests.

Potuz identifies that Teku will be the most affected client by these changes and wants to ensure they're on board with the approach. There's also mention that some of Terence's previous work that hasn't merged yet will need to be discarded due to these changes.

## Participants

- .paulharris
- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-14T00:12:19.818000+00:00] .paulharris: i do wonder if it'd actually be better keeping builders out of the validators list and then the churn wont increase the indices, but its a big change... it'd make it easier to reason about whats doing validator duties so that'd kinda be nice, but there's a lot of things to consider if we do that... just a thought i had while reading some of this because we're talking about avoiding it but it might actually be simpler... anyway i'll review the prs, you guys have probably thought about and excluded that as reasonable already...
[2025-12-14T00:15:23.941000+00:00] jtraglia: That’s what we’re doing 😄
[2025-12-14T00:17:21.512000+00:00] .paulharris: oh 🙂 ok then yay 🙂 i was behind it seems heh
[2025-12-14T00:56:11.789000+00:00] potuz: wow you actually managed to write two PRs with passing tests?!?! I don't know if that's a good thing or a bad thing 😅 . Option 2 seems better to me, reviewing that one
[2025-12-14T01:00:27.309000+00:00] jtraglia: Ha yeah. The 2nd one was a lot easier. I just copied everything, made a few spec changes, and reverted a bunch of tests.
[2025-12-14T01:01:43.021000+00:00] potuz: impressive to get tests passing, thanks, barring some implementation blowup in complexity I doubt there' s any reaction against these changes.
[2025-12-14T01:02:42.135000+00:00] potuz: Teku will be the most affected I think to probably want to make sure they are on board cc <@504202741933932544> <@641548687813902336>
[2025-12-14T01:03:01.368000+00:00] potuz: Terence did a lot of work that hasn't merged and will go to trash too
```

</details>
