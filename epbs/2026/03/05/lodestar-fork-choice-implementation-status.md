# Lodestar Fork Choice Implementation Status

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The Lodestar team has implemented only a bare minimum fork choice mechanism for devnet 0, with 0xunclebill describing the current implementation as being "held together with scotch tape and LLM hallucinations." The attestation data index functionality was hastily added at the last minute, and the team acknowledges that fork choice and attestation-related issues aren't useful for debugging at this stage given the implementation's current state.

The team plans to complete their full fork choice implementation by devnet 1. There appears to be a potential bug in how attestation weight is handled, as their current approach prioritizes blocks with payloads to extend the chain but may not be properly accounting for attestation weight in the fork choice rule.

## Participants

- 0xunclebill

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T12:46:47.368000+00:00] 0xunclebill: anything regarding fork choice or attestations isn’t really useful for us to debug atm. we wrote the bare minimum fork choice implementation because that was the plan for devnet 0 as far as I understood from previous discussions. The attestation data index stuff I hacked at the last minute 

By devnet 1 we’ll have our full fork choice impl
[2026-03-05T12:50:03.473000+00:00] 0xunclebill: Pretty much our current fork choice impl is literally held together with scotch tape and LLM hallucinations
[2026-03-05T12:53:23.443000+00:00] 0xunclebill: I think we prioritize blocks with payloads to extend the chain, we shouldn’t be completely ignoring attestation weight but maybe there’s a bug there
```

</details>
