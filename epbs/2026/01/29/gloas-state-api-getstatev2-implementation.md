# Gloas State API getStateV2 Implementation

**Channel:** epbs | **Date:** 2026-01-29

## Summary

nc1234 raised a technical question about the Gloas State API's `getStateV2` implementation, specifically asking whether it should return the post-state (after `state_transition()`) or the execution payload state (after `process_execution_payload`) when called with a slot parameter. The same question applies to requests for head/finalized/justified block states.

.paulharris responded that this behavior needs to be formally defined, suggesting they should likely continue returning the post-state to maintain consistency with the current API design. They noted having previously commented on the API issue and proposed that execution payload states could be fetched using the state root of that specific state. An important open question remains about what the `/finalized` endpoint should return, which .paulharris emphasized as significant since it relates to checkpoint states.

## Participants

- .paulharris
- nc1234

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-29T04:54:30.983000+00:00] nc1234: <@586161934425128960> <@884836500037066862><@680234424037670912>   In Gloas, if we call `getStateV2` with `slot`, does it return the post-state (the one after state_transition()) or does it return the execution payload state (the one after process_execution_payload) if payload is available? I guess similar question if we are asking for state of head/finalized/justified block
[2026-01-29T19:19:15.997000+00:00] .paulharris: we'll have to define that, i think given the way the api is now, we should probably continue to return the post state... i commented on the api issue on monday iirc. the execution payload state would probably be fetched using the state root of that state... im not sure what /finalized should return, thats pretty important as a checkpoint state
```

</details>
