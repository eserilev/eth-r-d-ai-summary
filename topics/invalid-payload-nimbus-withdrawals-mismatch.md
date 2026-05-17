# Invalid payload and Nimbus withdrawals mismatch

---

## 2026-05-13 (epbs)

A user (tuyen4818) reported discovering an invalid payload on slot 49700 of the glamsterdam-devnet-3 testnet, suspected to originate from a Nimbus client. The error logs show a "Withdrawals mismatch" during envelope verification, where the actual payload hash (0x9e3ecb1b43eb0e4d2f31095adb896a0689d3ef7640b95ab334e49532d8d5d6a8) differed from the expected hash (0x3da34ac4371227edfbf6f74375e1b47c3541c4856d3668ffca204505e731d557), causing the Lodestar client to reject the execution payload during gossip processing.

A team member (tomi0x) confirmed this is a known issue within Nimbus and indicated that they are currently working on implementing a fix. No timeline for the resolution was provided, leaving this as an ongoing action item for the Nimbus development team.

**Participants:** tomi0x, tuyen4818

<details>
<summary>Raw messages</summary>

```
[2026-05-13T13:26:44.248000+00:00] tuyen4818: found this invalid payload, probably from Nimbus https://dora.glamsterdam-devnet-3.ethpandaops.io/slot/49700
```
May-13 13:22:24.741[chain]         ^[[36mverbose^[[39m: Completed getBlobsV2 for block slot=49700, root=0xced478320d49430802c5176a9a756a2c2f814017679c3dee71d2346fd22780e6
May-13 13:22:25.014[chain]            ^[[33mwarn^[[39m: Error processing execution payload from gossip slot=49700, peer=16Uiu2HAkyYzoKommYCsEofJk7PzssLtTFQ7G36UZu7ZsbYZqQbwk, root=0xced478320d49430802c5176a9a756a2c2f814017679c3dee71d2346fd22780e6 - Envelope verification error: Withdrawals mismatch between payload and expected payload=0x9e3ecb1b43eb0e4d2f31095adb896a0689d3ef7640b95ab334e49532d8d5d6a8 expected=0x3da34ac4371227edfbf6f74375e1b47c3541c4856d3668ffca204505e731d557
Error: Envelope verification error: Withdrawals mismatch between payload and expected payload=0x9e3ecb1b43eb0e4d2f31095adb896a0689d3ef7640b95ab334e49532d8d5d6a8 expected=0x3da34ac4371227edfbf6f74375e1b47c3541c4856d3668ffca204505e731d557
    at BeaconChain.importExecutionPayload (file:///usr/src/lodestar/packages/beacon-node/src/chain/blocks/importExecutionPayload.ts:159:11)
    at BeaconChain.processExecutionPayload (file:///usr/src/lodestar/packages/beacon-node/src/chain/blocks/importExecutionPayload.ts:296:3)
    at JobItemQueue.runJob (file:///usr/src/lodestar/packages/beacon-node/src/util/queue/itemQueue.ts:149:22)
```
[2026-05-13T13:27:31.086000+00:00] tomi0x: this is a known issue within nimbus
[2026-05-13T13:27:36.937000+00:00] tomi0x: we are working on a fix
```

</details>

