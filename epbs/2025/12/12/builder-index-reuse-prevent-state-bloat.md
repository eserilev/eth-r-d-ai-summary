# Builder index reuse to prevent state bloat

**Channel:** epbs | **Date:** 2025-12-12

## Summary

fradamt proposed implementing builder index reuse as a mechanism to prevent permanent state bloat in Ethereum. The core idea is that indices would be reusable, making it impossible to permanently bloat the state without keeping capital locked up that earns no yield, creating an economic disincentive for state bloat.

They suggested this should be relatively simple to implement for builders since there's no slashing mechanism involved, unlike validators where similar efforts may have been more complex. However, fradamt acknowledged not having looked into previous validator index reuse efforts in detail.

The discussion appears to be seeking input from the broader R&D community (indicated by the role pings) but no responses or consensus were captured in these messages, leaving the proposal as an open idea for further discussion and evaluation.

## Participants

- fradamt

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-12T23:10:31.746000+00:00] fradamt: It would be worth it to build in reusing of indices, so that it's actually impossible to permanently bloat the state without keeping capital locked up earning no yield
[2025-12-12T23:16:05.127000+00:00] fradamt: I never looked into the efforts to do this for validators, but presumably it should be quite simple here since there's  no slashing?
[2025-12-12T23:19:45.397000+00:00] fradamt: <@&595685821026271242>
[2025-12-12T23:22:43.584000+00:00] fradamt: <@&595685821026271242>
```

</details>
