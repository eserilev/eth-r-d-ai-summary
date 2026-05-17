# EL Block Building Limits and Deposit Restrictions

**Channel:** epbs | **Date:** 2026-05-08

## Summary

The discussion centers around implementing deposit limits at the Execution Layer (EL) block building level versus using a separate queue system. The main debate focuses on whether to set the limit at 16 or 512 deposits per block, with significant tradeoffs for each approach. A limit of 16 deposits is seen as extremely restrictive and vulnerable to queue hogging attacks, where an attacker could monopolize the deposit queue with just 32 ETH by continuously depositing and exiting validators. At 512 deposits, such attacks become more expensive but raise concerns about signature verification performance.

The participants also discuss a separate "top-up deposit issue" where someone could submit 8193+ single ETH deposits to existing validators, which wouldn't be addressed by the proposed builder deposit restrictions. barnabasbusa suggests that limiting all deposit types on the EL side to a maximum of 512 deposits per block could solve multiple issues simultaneously, eliminating the need for a separate queue while allowing the mempool to handle deposit prioritization naturally.

The cost analysis reveals that even with a 64-epoch withdrawal delay for builders, a queue hogging attack would only require about 1024 ETH with a 16-deposit limit, making it relatively affordable for determined attackers. The discussion remains unresolved regarding the optimal approach and specific parameter values.

## Participants

- barnabasbusa
- m.kalinin
- parithosh

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-05-08T07:41:56.910000+00:00] parithosh: wouldn't it be easier to enforce the limit on the EL block building logic instead of a second queue? if we have over x deposits in a block then consider it invalid?
[2026-05-08T07:52:32.728000+00:00] m.kalinin: it depends on the limit
[2026-05-08T07:52:44.878000+00:00] m.kalinin: if it's a 512 it might still be high. If it's 16 then it's probably fine from sig verification perspective, but quite restrictive from the deposit inclusion perspective
[2026-05-08T08:27:28.588000+00:00] barnabasbusa: with 16 we can easily see someone just hogging the queue all the time
[2026-05-08T08:27:42.523000+00:00] barnabasbusa: with 512 the hogging becomes a lot more expensive
[2026-05-08T08:28:25.356000+00:00] m.kalinin: are we fine with 512 sig verifications per block?
[2026-05-08T08:29:13.865000+00:00] barnabasbusa: 16 is extremly limiting
[2026-05-08T08:29:26.301000+00:00] barnabasbusa: that means with 32 eth you can hog that queue non stop?
[2026-05-08T08:29:56.750000+00:00] barnabasbusa: you setup a dummy bot and keep depositing 16 new builders, then exit them in the following epoch then rinse and repeat
[2026-05-08T08:30:38.367000+00:00] barnabasbusa: also none of this fix the top up deposit issue
[2026-05-08T08:30:57.287000+00:00] m.kalinin: what is the issue?
[2026-05-08T08:30:59.604000+00:00] barnabasbusa: where we still gonna be shit out of luck if anyone does  8193x1ETH top up requests
[2026-05-08T08:31:12.520000+00:00] m.kalinin: ah, that's a different thing
[2026-05-08T08:31:17.440000+00:00] barnabasbusa: yeah
[2026-05-08T08:36:38.862000+00:00] barnabasbusa: but if we end up limiting all deposit types on the EL side, then that could solve the same issues
[2026-05-08T08:37:38.857000+00:00] barnabasbusa: if we say on the EL side we want at most 512 deposits bundled in a deposit, then we don't need a queue, don't need to worry about top up deposits reaching 8193+, or how much time it would take for signature verification to take place and the mempool could take care of the priority of such deposits
[2026-05-08T08:48:47.103000+00:00] m.kalinin: `MIN_BUILDER_WITHDRAWABILITY_DELAY=64`, so it's there going to be 64 epochs to wait till you can withdraw, if you don't want to wait then you have to have a budget for 64 epochs, which is `64*32*MAX_DEPOSIT_REQUESTS_PER_PAYLOAD` ETH, looks quite expensive. What is the benefit of such an attack?
[2026-05-08T08:49:27.237000+00:00] barnabasbusa: `64*16*1`ETH no?
[2026-05-08T08:49:33.689000+00:00] barnabasbusa: thats only 1024 ETH
[2026-05-08T08:49:53.792000+00:00] m.kalinin: 16 per block
[2026-05-08T08:50:05.475000+00:00] barnabasbusa: `MAX_PENDING_BUILDER_DEPOSITS_PER_EPOCH`
[2026-05-08T08:50:09.643000+00:00] barnabasbusa: this is the new flag you proposed
[2026-05-08T08:50:16.113000+00:00] barnabasbusa: per_epoch
[2026-05-08T08:50:51.141000+00:00] m.kalinin: this is a different thing, if we limit to 16 per payload, not to 512 without introducing a separate queue
```

</details>
