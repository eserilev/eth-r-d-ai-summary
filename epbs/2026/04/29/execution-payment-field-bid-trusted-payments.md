# Trusted payments policy and client implementation decisions

**Channel:** epbs | **Date:** 2026-04-29

## Summary

This discussion centers on the implementation of trusted payments in Ethereum clients, referencing previous extensive discussions with external parties that concluded trusted payments should be allowed at the protocol level. The participants agree that while the protocol should keep the door open for trusted payments, individual clients can still make their own decisions about default behavior and implementation approaches.

There's consensus that clients don't need to have uniform default behavior regarding trusted payments. One suggested approach is for clients to require explicit opt-in through flags like `--allow-dangerous-trusted-payments` when configuring non-peer-to-peer bids, while some clients could choose to only allow trustless (p2p) bids entirely. The discussion reflects a desire to maintain protocol flexibility while allowing client-level discretion in how trusted payments are handled.

## Participants

- fradamt
- jtraglia
- nflaig

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-29T07:41:06.914000+00:00] fradamt: We discussed this a lot months ago here and in breakouts (including with external parties) and the decision was that they should be allowed. I don't think this should be renegotiated lightly
[2026-04-29T08:17:25.657000+00:00] nflaig: yes agree, talked with a bunch of people and it might be a good idea to keep the door open to have trusted payments, at least from the protocol perspective and we can still think about what the default behavior should be for clients
[2026-04-29T08:23:57.140000+00:00] jtraglia: Re trusted payments in clients, I don't think there needs to be a default behavior. I think it's perfectly acceptable to say X client will only allow trustless (p2p) bids. One fun idea would be a required `--allow-dangerous-trusted-payments` flag (or something like that) in order to configure non-p2p bids.
```

</details>
