# EPBS Implementation and Devnet Configuration

**Channel:** epbs | **Date:** 2026-02-23

## Summary

The discussion focused on technical implementation details for an EPBS (Enshrined Proposer-Builder Separation) devnet configuration. stefanbratanov identified that the ExecutionPayloadV4 specification includes a blockAccessList field and suggested sending null values for EPBS implementation. nflaig confirmed that the geth EPBS devnet image works correctly when passing just the slot number.

The conversation then shifted to devnet client configuration, with nflaig proposing a two-client setup using Geth with both Lighthouse and Lodestar consensus clients (2 nodes each), configured with the Gloas fork at epoch 1. parithosh humorously suggested adding a third client using auction terminology, though pk910 reiterated the two-client approach.

The team appears to be preparing to launch an EPBS devnet with the discussed configuration, though no final decision was explicitly stated about whether to proceed with two or three client implementations.

## Participants

- nflaig
- parithosh
- pk910
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-23T10:22:24.766000+00:00] stefanbratanov: I noticed we have https://github.com/ethereum/execution-apis/blob/main/src/engine/amsterdam.md#executionpayloadv4 with blockAccessList, I suppose we just send null for epbs
[2026-02-23T10:50:59.681000+00:00] nflaig: it seems like the geth image `epbs-devnet-0` is happy with just passing the slot number
[2026-02-23T13:04:00.944000+00:00] nflaig: make it two clients
```yaml
participants:
  - el_type: geth
    el_image: ethpandaops/geth:epbs-devnet-0
    cl_type: lighthouse
    cl_image: ethpandaops/lighthouse:epbs-devnet-0
    count: 2
  - el_type: geth
    el_image: ethpandaops/geth:epbs-devnet-0
    cl_type: lodestar
    cl_image: ethpandaops/lodestar:epbs-devnet-0
    count: 2

network_params:
  gloas_fork_epoch: 1

additional_services:
  - dora
```
[2026-02-23T13:19:40.689000+00:00] parithosh: https://giphy.com/gifs/Fellows-Auctioneers-auction-fellows-fellowsauction-6rJuKorjRJmoqSz4RU
[2026-02-23T13:19:47.585000+00:00] parithosh: Do i hear a 3rd client?
[2026-02-23T13:20:02.401000+00:00] parithosh: We can hit on sold and launch a devnet then 😄
[2026-02-23T22:06:43.264000+00:00] pk910: make it two clients
```

</details>
