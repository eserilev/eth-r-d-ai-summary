# Miscellaneous

---

## 2026-03-20 (epbs)

This discussion contains only a single character message "t" from potuz with no technical content, decisions, or substantive discussion to summarize.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-03-20T13:20:40.528000+00:00] potuz: t
```

</details>

---

## 2026-03-27 (epbs)

This discussion contains no substantive technical content. The conversation consists of empty messages from jtraglia and nflaig, with terencechain sharing a Dune-themed GIF expressing disbelief. No technical points, decisions, or action items were discussed.

**Participants:** jtraglia, nflaig, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-03-27T14:00:13.464000+00:00] jtraglia: 
[2026-03-27T14:04:10.902000+00:00] terencechain: https://tenor.com/view/dune-dune-part-2-stilgar-fremen-disbelief-gif-17405899726704623697
[2026-03-27T15:10:31.942000+00:00] nflaig: 
```

</details>

---

## 2026-04-02 (epbs)

This discussion contains only a single message from kewei9453 stating "Same question" without any preceding context or additional messages. There are no technical points, decisions, or action items that can be determined from this isolated message. The nature of the question being referenced is unclear from the available information.

**Participants:** kewei9453

<details>
<summary>Raw messages</summary>

```
[2026-04-02T11:50:29.294000+00:00] kewei9453: Same question
```

</details>

---

## 2026-04-05 (epbs)

The discussion begins with potuz catching up on work during a holiday (Easter, based on the egg hunting reference) and discovering a bug in Prysm's REST endpoint handler. After analyzing the issue, potuz determines that while the current behavior isn't problematic in general and wouldn't be bad if payload content was tracked on finalization, it's broken specifically on Gloas (likely a testnet or development network).

Potuz creates a fix in pull request #16635 on the OffchainLabs/prysm repository and notes that the changes are already available on the devnet branch. However, they express concern that restarting nodes with the fix might not work immediately and that they may need to sync up a node first to serve it properly.

0xunclebill offers to test the changes using a local Prysm node for syncing, but potuz points out a potential circular problem - if the Prysm node itself can't sync due to the bug, testing becomes more challenging. The conversation ends with this unresolved concern about the testing approach.

**Participants:** 0xunclebill, potuz

<details>
<summary>Raw messages</summary>

```
[2026-04-05T18:58:05.058000+00:00] potuz: Just catching up on this channel, which screwed up country makes people work the day kids are hunting for saturated fat in the form of eggs
[2026-04-05T19:26:31.713000+00:00] 0xunclebill: no rush enjoy the holiday!
[2026-04-05T19:29:03.492000+00:00] potuz: yeah I can see the handler for the REST endpoint is fucked up, I'll cook a fix.
[2026-04-05T19:33:12.128000+00:00] potuz: This behavior would not be bad currently, and would in principle not be bad if we actually tracked the payload content on finalization, but on Gloas this is just broken. I think we don't hit this error because we take the slot of the state as given and anyway requests payloads from before to decide if we sync from it or not
[2026-04-05T19:40:44.418000+00:00] potuz: hopefully this fixes this. https://github.com/OffchainLabs/prysm/pull/16635
[2026-04-05T19:47:18.993000+00:00] potuz: Although I'm afraid I'll have to sync up a node with this to be able to serve it to you to start with
[2026-04-05T19:50:39.918000+00:00] potuz: it's on the devnet branch already, but I'm afraid that a restart probably won't work straight away. We'll see
[2026-04-05T20:00:35.270000+00:00] 0xunclebill: no worries I’m running a Prysm node locally that I’m using to sync against. will try it out with the changes in ur branch
[2026-04-05T22:57:39.821000+00:00] potuz: Problem is if that Prysm node can't sync lol
```

</details>

---

## 2026-04-21 (epbs)

This brief discussion consists of minimal interaction, with barnabasbusa posting an eyes emoji (👀) followed by tagging two users with a "no pressure" comment. No technical content, decisions, or specific topics were discussed in these messages. The nature of what the tagged users are being referenced about remains unclear from the available context.

**Participants:** barnabasbusa

<details>
<summary>Raw messages</summary>

```
[2026-04-21T12:30:30.491000+00:00] barnabasbusa: 👀
[2026-04-21T12:37:34.279000+00:00] barnabasbusa: <@586161934425128960> <@358120958726373381> no pressure
```

</details>

---

## 2026-05-05 (epbs)

This appears to be a brief, incomplete exchange where potuz was attempting to request some rewards from another user but was interrupted mid-message. Potuz then expressed frustration about someone else being faster at typing, suggesting they may have been competing to claim or request something time-sensitive.

No technical details, decisions, or action items were discussed in this fragment of conversation.

**Participants:** potuz

<details>
<summary>Raw messages</summary>

```
[2026-05-05T13:51:21.822000+00:00] potuz: Hey <@1297482247695499278> can I get those rewards? I
[2026-05-05T13:51:28.942000+00:00] potuz: ahh crap someone is faster than me typing
```

</details>

