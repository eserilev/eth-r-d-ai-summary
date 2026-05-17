# Builder API Design and Registration

**Channel:** epbs | **Date:** 2025-12-15

## Summary

The discussion focused on the design of the Builder API, specifically whether gas limit and fee recipient parameters should be provided during the `get_bid` call or through pre-registration. While potuz initially preferred avoiding the complexity of a separate registration call, builders (particularly gd_0) emphasized the practical necessity of having gas limit and fee recipient information beforehand to properly build payloads and handle trusted payments, which require reserving gas for proposer payment transactions.

A key challenge emerged around trusted payments, where proposers need different trust levels for different builders, making this a per-builder rather than global preference. The team explored using a gossip topic to communicate proposer preferences but concluded that builder-specific settings (like trusted payment acceptance) require individual registration. Privacy concerns were also raised about validator registrations potentially exposing all of a node's validators to builders.

The discussion concluded with potuz proposing a minimal three-method Builder API: `getBid` for requesting bids, `postBlock` for block dissemination, and `registerProposer` for epoch E+1 proposers to communicate their preferences (fee recipient, gas limit, trusted payment acceptance) at the beginning of epoch E. An action item was identified to bring the gas limit parameter discussion to ACD/T (All Core Devs/Testing) for broader community input, particularly regarding whether this should be handled by consensus layer or execution layer clients.

## Participants

- bharath.vedartham
- gd_0
- nflaig
- potuz
- tersec

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-15T09:21:00.048000+00:00] nflaig: When calling `get_bid` what's the reason for providing gas limit and fee recipient there instead of in a pre-registration as we do now? For fee recipient I can see the argument that you only need to set it in the bid now and the payment tx no longer needs to be part of payload but we also keep supporting trusted payments so builders need to know it beforehand. And for gas limit I also think it's to late to communicate that when calling the api as payloads are prepared in previous slot already
[2025-12-15T09:28:09.889000+00:00] potuz: We can ask builders but I don't believe payloads are prepared in the previous slot already. They seal it in milliseconds and I believe anyway they will seal the payload on demand when receiving the call. I am not strongly against the registration. Just that if I can avoid another call and the extra complexity that comes from it I'm happy to.
[2025-12-15T09:29:06.881000+00:00] potuz: The registration has the advantage that it can be taken out of the hot path
[2025-12-15T09:30:11.415000+00:00] potuz: And if we go with <@592004585506340885>'s idea of having a gossip Topic to gossip preferences then I'd just add the bool to signal if they will accept trusted payments and that's it
[2025-12-15T09:30:28.619000+00:00] potuz: We can remove these from the builder API
[2025-12-15T09:31:56.092000+00:00] bharath.vedartham: will the proposer want to accept trusted payments from all builders? i would assume a proposer would only trust a subset of the builders staked to accept a trusted payment.
[2025-12-15T09:32:19.286000+00:00] potuz: Yes and only those he calls
[2025-12-15T09:32:34.772000+00:00] bharath.vedartham: I guess we could say that, the proposer would only whitelist builder urls that he trusts
[2025-12-15T09:32:44.528000+00:00] bharath.vedartham: for trusted payments
[2025-12-15T09:33:36.333000+00:00] bharath.vedartham: does it also makes sense to have a req/resp for the gossip topic? i would assume a builder that becomes active after the proposer has gossiped the preferences would want to fetch the preferences from a peer?
[2025-12-15T09:34:21.528000+00:00] potuz: Yeah but you're right though, this undermines a few points, and preferences will vary per builder
[2025-12-15T09:34:50.183000+00:00] nflaig: yeah should get more feedback from builders on this, the gossip topic might be a good solution, there have been issue with register validators api due to the amount of data being sent if you have a lot of keys and mostly unncessary to do this for all validators each epoch.

I just realized something though, if the CL acts as a builder there is currently no way to tell the EL which gas limit to use, the only way is to set a flag on the EL side afaik
[2025-12-15T09:35:22.745000+00:00] bharath.vedartham: even tho the proposers do gossip preferences every epoch, the builder could either wait till the next epoch to receive all the preferences and start building then
[2025-12-15T09:36:22.286000+00:00] bharath.vedartham: maybe a dumb question, but is `gas_limit` necessary to specify? why would a proposer want to restrict the `gas_limit` of a block being built by an external builder. i can understand this while self building
[2025-12-15T09:37:11.731000+00:00] potuz: This is a good point and we may need to bring this up to ACD/T. I know of at least <@316280621783580673> that wants this to go the other way and remove this from the CL
[2025-12-15T09:37:57.262000+00:00] potuz: Gas limit is decided by the community, otherwise builders would decide it
[2025-12-15T09:38:43.353000+00:00] potuz: Proposers should enforce the gas limit is the one promised in the bid and the one they requested
[2025-12-15T09:39:59.937000+00:00] tersec: the registration endpoint also has had a chronic issue with, how many validators can be registered at once. It's not specified, never has been, and the status quo is the result of informal negotiations between clients and the server software
[2025-12-15T09:40:52.913000+00:00] potuz: Registration, if it happens, should be only for the proposers of E+1 when computing the lookahead
[2025-12-15T09:41:04.580000+00:00] potuz: Same as the gossip topic
[2025-12-15T09:42:00.317000+00:00] gd_0: it's quite important that we get the registration before hand. Primarily so that we know what the gas limit is when building.
If we don't have the gas_limit preference we'd have to assume the block gas limit will be the minimum possible for that block.

If we are using trusted payments we are also required to attach a proposer payment as the last tx. In this case we need the proposer fee recipient ahead of time so we can simulate how much gas it will use to make the transfer (this varies if the fee recipient is a contract). We then can reserve this gas from the gas limit.
[2025-12-15T09:43:25.251000+00:00] potuz: Gas limit and fee recipient may be fetched from the gossip topic. The biggest issue is trusted payments being accepted or not
[2025-12-15T09:45:28.958000+00:00] gd_0: Would definitely also be good to have that as part of the gossip topic so we know if we need to reserve gas for the payment or not
[2025-12-15T09:45:57.487000+00:00] bharath.vedartham: it seems to me that the biggest drawback of validator registrations it the volume of data being sent. If we specify that only proposers of the upcoming epoch send registrations. we can use validator registrations to communicate preferences per builder too i believe? this can give more flexibility to proposers going forward?
[2025-12-15T09:46:31.972000+00:00] bharath.vedartham: preferences like whether the proposer accepts trusted EL payments, minimum acceptable bid etc? and these registrations could be customized per builder?
[2025-12-15T09:46:57.373000+00:00] potuz: But the problem is what <@1386598056320831509> mentioned: rational proposers will contact directly builders that they may not take trusted payments from
[2025-12-15T09:47:15.726000+00:00] potuz: So this needs to be signaled on a builder basis, not on a global topic
[2025-12-15T09:49:04.723000+00:00] nflaig: imo should be done by CL and, specifically the vc should handle the configuration as it's the best place to configure per pubkey, the EL as no context about connected validators

there was a PR https://github.com/ethereum/execution-apis/pull/608 to pass gas limit in payload attributes
[2025-12-15T09:49:54.023000+00:00] potuz: Yeah this topic definitely needs to be brought to ACD for discussion
[2025-12-15T09:51:11.036000+00:00] tersec: More generally, whichever components do the signing have to care. Today, that means web3signer, for example, too
[2025-12-15T09:51:31.169000+00:00] potuz: There's a good argument to keep it in the EL and it's that some L2s don't use the CL
[2025-12-15T09:52:34.355000+00:00] potuz: But I also think that for CL clients to act as builders they need to be able to drive this on demand on the Payload attributes call
[2025-12-15T09:53:51.126000+00:00] tersec: Well, builders in a self-building sense or as builders for others? Those are somewhat distinct roles
[2025-12-15T09:54:04.292000+00:00] tersec: If they want to bid, of course
[2025-12-15T09:54:17.054000+00:00] potuz: Both, but the latter is more important
[2025-12-15T09:54:47.998000+00:00] potuz: We have to implement the latter and we will be building for other keys that may have different gas limits requests than ours
[2025-12-15T09:54:50.964000+00:00] tersec: If they're self-building the whole thing is internal
[2025-12-15T09:55:01.978000+00:00] tersec: Any "protocol" can be notional
[2025-12-15T09:57:59.611000+00:00] bharath.vedartham: this is something we could solve with validator registrations i believe. the proposer can communicate per builder preferences via the registrations. 
we could also communicate the builder index in the gossip topic and communicate builder preferences over there but that doesn't seem robust and highly prone to sybil attacks.
[2025-12-15T09:59:24.322000+00:00] bharath.vedartham: i believe we could enforce builders to accept validator registrations only from proposers for epoch E+1 using the lookahead? the beacon client would only have to send registrations if it contains validator keys that are going to be proposing in the upcoming epoch E+1
[2025-12-15T10:00:33.689000+00:00] bharath.vedartham: are there any other pain points clients have with validator registrations in general outside of the heavy data it has to send per epoch?
[2025-12-15T10:01:45.336000+00:00] tersec: Historically, there have been privacy/correlation issues with it
[2025-12-15T10:11:27.175000+00:00] bharath.vedartham: what do you mean by correlation issues?
[2025-12-15T10:12:06.965000+00:00] tersec: well, it's the one place in the protocol that up front and trivially, a protocol actor can find out all of a node's validators, all at once
[2025-12-15T10:12:35.209000+00:00] bharath.vedartham: got it that makes sense 👍
[2025-12-15T14:04:41.511000+00:00] bharath.vedartham: this might have to be a per builder value. a proposer can connect to a builder who it doesn't want a trustless payment from
[2025-12-15T14:04:48.395000+00:00] bharath.vedartham: i think validator registrations will be the place to have this
[2025-12-15T14:38:30.884000+00:00] potuz: Nah that was a bad idea, this flag needs to be chosen per-builder, so this has to be on a registration basis it seems
[2025-12-15T14:40:47.895000+00:00] potuz: It seems that the minimal practical builder API has three methods
- getBid. Sent by the proposer to request a bid. sends a signed message to the builder, includes proposer index, slot, parent root and parent hash. 
- postBlock. Sent by the proposer to just forward the full signed beacon block, builder helps in dissemination and gets an early acknowledgment their bid was selected. 
- registerProposer. Sent by the proposers of the **next** epoch E+1 at the beginning of E. This includes fee recipient, gas limit, if they accept trusted payments, etc.
```

</details>
