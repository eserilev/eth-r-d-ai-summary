# Attack Economics and Valid vs Invalid Signatures

**Channel:** epbs | **Date:** 2026-05-08

## Summary

This discussion focuses on an Ethereum attack vector involving deposit requests with valid versus invalid signatures. The core issue identified is that attacks using valid signatures are essentially "free" because attackers can recover their capital by withdrawing their deposits after a few epochs, requiring only access to large amounts of ETH (around $20M for 8K ETH) rather than actually spending it. This makes the attack economically viable for wealthy entities who can repeatedly execute it across multiple slots.

The conversation clarifies that invalid signature deposits pose less of a threat because the EL burns that money permanently, making them costly attacks. However, valid signatures create a more serious problem since they allow capital recovery. The attack's effectiveness stems from signature verification delays - even 1.23 seconds of verification time for mixed valid/invalid signatures can cause block reorgs, and subsequent blocks must include the same requests indefinitely, creating persistent network disruption.

The discussion reveals tension around proposed limit increases, with potuz questioning why limits would be raised given these attack vectors. An open question remains about the practical impact duration and whether current verification times are sufficient to cause meaningful network disruption.

## Participants

- jtraglia
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T00:24:38.802000+00:00] potuz: The biggest problem is that the attack is essentially free. It's just the gas spent unless we act fast to slash them
[2026-05-08T00:25:01.591000+00:00] potuz: You need to have a lot of capital but you recover it
[2026-05-08T00:25:09.717000+00:00] potuz: So any large group can trigger it
[2026-05-08T00:25:48.552000+00:00] jtraglia: Couldn't a malicious builder include deposit requests with invalid signatures for free? Up to whatever the limit is.
[2026-05-08T00:26:01.638000+00:00] jtraglia: So the `AllBad` case might need to be taken serious.
[2026-05-08T00:34:12.623000+00:00] potuz: Invalid signatures is not an attack
[2026-05-08T00:34:16.406000+00:00] potuz: Valid ones are
[2026-05-08T00:34:50.988000+00:00] potuz: I don't really care about someone that can burn 8K ETH per block
[2026-05-08T00:35:18.275000+00:00] potuz: The problem is that you can use 8K ETH and withdraw it in a few epochs
[2026-05-08T00:38:35.112000+00:00] jtraglia: Is this because these would be rejected before the signature checks?
[2026-05-08T00:39:04.932000+00:00] potuz: No, because invalid signature deposits are burned money
[2026-05-08T00:39:11.575000+00:00] potuz: The EL doesn't return it
[2026-05-08T00:39:24.384000+00:00] jtraglia: Either way. $20m really is not _that_ much to a company/attacker/wealthy_person/nation_state.
[2026-05-08T00:39:27.915000+00:00] potuz: Valid ones are the problem
[2026-05-08T00:39:52.620000+00:00] potuz: And what I'm arguing is that you don't need to "spend" 20M
[2026-05-08T00:39:56.490000+00:00] potuz: You just need to have it
[2026-05-08T00:40:02.069000+00:00] potuz: The attack is free
[2026-05-08T00:40:03.606000+00:00] jtraglia: Yes, that's what I'm saying too.
[2026-05-08T00:40:41.222000+00:00] jtraglia: Okay sorry. We agree.
[2026-05-08T00:41:19.799000+00:00] jtraglia: But even then, $20m burned to cause a big network issue? Could be worth it.
[2026-05-08T00:41:43.767000+00:00] jtraglia: Would this just cause a few missed slots or worse?
[2026-05-08T00:42:15.235000+00:00] jtraglia: Say it takes 20 seconds to verify 8k invalid signatures.
[2026-05-08T00:45:48.640000+00:00] potuz: It's not burned!
[2026-05-08T00:45:53.281000+00:00] potuz: You recover it
[2026-05-08T00:46:02.043000+00:00] potuz: So you trigger it again
[2026-05-08T00:46:12.981000+00:00] potuz: And if you have 80m you do it constantly
[2026-05-08T00:46:21.228000+00:00] potuz: Every single slot
[2026-05-08T00:46:35.453000+00:00] potuz: Probably need a few more millions but you get the point
[2026-05-08T00:47:03.600000+00:00] potuz: Gas is cheap, cause you don't need to compete for inclusion
[2026-05-08T00:47:20.034000+00:00] jtraglia: Didn't you just say it's burned here?
[2026-05-08T00:47:35.823000+00:00] potuz: Only on invalid ones
[2026-05-08T00:47:42.705000+00:00] potuz: If I attack I'll send valid ones
[2026-05-08T00:47:44.022000+00:00] jtraglia: Verifying invalid signatures is slower. That is what I'm talking about here.
[2026-05-08T00:47:49.123000+00:00] potuz: And withdraw
[2026-05-08T00:48:03.342000+00:00] jtraglia: Yeah I understand that attack too.
[2026-05-08T00:48:12.289000+00:00] potuz: You only need to send one invalid and 8K valid if you want to grief that
[2026-05-08T00:48:31.977000+00:00] jtraglia: But according to <@363800010518822915>, that might only take a few seconds to verify. It's not even an attack.
[2026-05-08T00:48:48.407000+00:00] potuz: More than 2 seconds reorgs the block
[2026-05-08T00:49:02.693000+00:00] jtraglia: 8191 valid + 1 invalid is still just 1.23 seconds.
[2026-05-08T00:49:23.799000+00:00] potuz: That's more than enough to reorg most blocks
[2026-05-08T00:49:40.873000+00:00] potuz: And the problem is that the next block needs to include the same requests
[2026-05-08T00:49:48.854000+00:00] potuz: Forever
[2026-05-08T00:50:54.957000+00:00] jtraglia: Then why are you suggesting we raise the limit?
```

</details>
