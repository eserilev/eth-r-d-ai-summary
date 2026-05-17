# Dynamic Builder Indices Management Complexity

**Channel:** epbs | **Date:** 2026-01-10

## Summary

Bharath Vedartham raised concerns about the complexity of implementing dynamic builder indices management in validator registration and bid request processes. The main challenge involves handling cases where builder indices change dynamically, which would require builders to expose endpoints returning lists of indices they're running or will submit bids from. This approach would force clients to integrate with these endpoints and maintain builder-to-indices caches, with unclear optimization around call frequency (potentially once per epoch or when sending registrations).

As an alternative approach to prevent replay attacks, Bharath suggested enforcing proposer signatures on validator registration requests, similar to the current getBid endpoint implementation. This would involve proposers signing over builder URLs for the validator registration endpoint, ensuring only the proposer can call the endpoint and potentially avoiding the complexity of dynamic builder indices management.

The discussion leaves open questions about whether the added complexity of dynamic indices management is worthwhile and seeks input on the feasibility of the signature-based alternative approach.

## Participants

- bharath.vedartham

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-01-10T16:40:14.514000+00:00] bharath.vedartham: the main probelm with adding builder indices to the registration and to the bid request is to handle cases where the builders indices change dynamically. For e.g in cases mentioned here: https://discord.com/channels/595666850260713488/692078615269212180/1458840067899527259
Builders can expose an endpoint where they can return a list of indices they are running or will submit bids from but now clients will have to integrate with this endpoint and maintain a builder -> builder indices cache etc. 
we also need to think about how frequently clients will call this endpoint, once an epoch? the epoch where one where they need to send the registration etc. i feel like it adds this complexity which i want to know if it is really worth it
[2026-01-10T16:45:04.383000+00:00] bharath.vedartham: to avoid replays,  could we also enforce proposers to sign the request to post validator registrations such that other builders can't call the registration endpoint like how we are doing now for the getBid endpoint? 
In the current version, we are making the proposer sign over the builder URL maybe we could do the same for the validator registration endpoint? so that only the proposer is able to call the endpoint?
```

</details>
