# Variable PTC Deadline Proposal

**Channel:** epbs | **Date:** 2026-04-23

## Summary

anderselowsson proposed a variable PTC (Preconfirmation Transaction Confirmation) deadline mechanism that could significantly improve Ethereum's scaling capabilities within the ePBS (enshrined Proposer-Builder Separation) framework. The proposal involves coupling the variable deadline with a single gas price per calldata byte, which would allow the system to dynamically adapt the deadline based on the aggregate calldata load across the network.

The proposal includes a specific design implementing "affine metering" alongside a unified calldata pricing model. According to anderselowsson, this approach could deliver substantial scaling improvements for Ethereum, with detailed analysis and illustrations provided in the linked Ethereum Research post.

No responses or further discussion occurred in this thread, leaving the proposal open for community review and feedback.

## Participants

- anderselowsson

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-23T14:34:05.138000+00:00] anderselowsson: A variable PTC deadline could significantly improve Ethereum scaling in ePBS, when coupled with a single gas price per calldata byte. This allows the deadline to adapt to the aggregate calldata load. This post proposes such a design and illustrates the scaling gains: https://ethresear.ch/t/the-case-for-a-variable-ptc-deadline-with-affine-metering-and-a-unified-calldata-price/24708
```

</details>
