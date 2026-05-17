# Variable PTC Deadline Proposal

---

## 2026-04-23 (epbs)

anderselowsson proposed a variable PTC (Preconfirmation Transaction Confirmation) deadline mechanism for Ethereum's ePBS (enshrined Proposer-Builder Separation) that could significantly improve scaling. The proposal involves coupling the variable deadline with a single gas price per calldata byte, allowing the deadline to dynamically adapt based on aggregate calldata load.

The proposal includes affine metering and unified calldata pricing as key components of the design. anderselowsson shared a detailed post on ethresear.ch that outlines the technical design and demonstrates the potential scaling improvements this approach could provide.

No other participants responded to the proposal in this discussion, leaving the technical merits, implementation challenges, and community reception as open questions requiring further dialogue.

**Participants:** anderselowsson

<details>
<summary>Raw messages</summary>

```
[2026-04-23T14:34:05.138000+00:00] anderselowsson: A variable PTC deadline could significantly improve Ethereum scaling in ePBS, when coupled with a single gas price per calldata byte. This allows the deadline to adapt to the aggregate calldata load. This post proposes such a design and illustrates the scaling gains: https://ethresear.ch/t/the-case-for-a-variable-ptc-deadline-with-affine-metering-and-a-unified-calldata-price/24708
```

</details>

