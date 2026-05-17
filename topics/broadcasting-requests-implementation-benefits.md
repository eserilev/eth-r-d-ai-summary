# Broadcasting requests twice and implementation benefits

---

## 2026-04-10 (epbs)

The discussion centers around a proposal to broadcast requests twice and include them in beacon blocks, with the main tradeoff being increased bandwidth usage in exchange for maintaining a single state transition model. **tbenr** views this as "significantly net-positive" since the double broadcasting appears unavoidable, and notes that while this duplication might be removable in the future, implementing a double-state concept would introduce lasting complexity and potential bugs in clients.

**potuz** highlights a key implementation benefit: removing the need to handle empty-full states from database/state generation components, which they consider "a big win." The participants agree this change should be orthogonal to fork choice logic, since whether a node has an attached state shouldn't affect fork choice decisions. However, **potuz** raises concerns about potential off-chain consensus issues, particularly around equivocations where attesters need to enforce payload completeness.

The discussion concludes with an action item to allocate time for examining potential impacts on fork choice and consensus mechanisms, and an open question from **tbenr** about why requests need to be blinded in bids, though this latter point isn't fully explored in the conversation.

**Participants:** potuz, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-04-10T14:01:33.248000+00:00] potuz: <@795439202732867594> could you weigh in the impact of broadcasting the requests twice and adding them to the beacon block?
[2026-04-10T14:02:03.416000+00:00] tbenr: it seems the double send of the request is unavoidable. so we are exchanging bandwidth with single state transition (with all the good consequences of it). I believe it is significantly net-positive.
[2026-04-10T14:08:00.150000+00:00] tbenr: and maybe we could remove this duplication in the future, on the other side, once the double-state concept lends on clients, the complexity\bugs damage would be done 🙂
[2026-04-10T14:10:34.132000+00:00] potuz: yeah I think the removal of the handling of empty-full states from db/stategen is a big win
[2026-04-10T14:11:02.393000+00:00] potuz: we should allocate time to see if it affects forkchoice or cosensus in any way
[2026-04-10T14:15:15.804000+00:00] tbenr: I have the feeling that this is orthogonal to FC though. If a node has a state attached or not should be irrelevant
[2026-04-10T14:26:42.269000+00:00] potuz: yeah, but for example offhchain issues like the above if there are equivocations and attesters need to enforce the payload being full, etc.
[2026-04-10T14:32:19.492000+00:00] tbenr: Ye but the verifiability of the payload seems unaffected. 

Can you recall me why requests need to be blinded in the bid?
```

</details>

