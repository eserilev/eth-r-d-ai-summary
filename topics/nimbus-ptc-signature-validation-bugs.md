# Nimbus PTC signature validation bugs

---

## 2026-03-18 (epbs)

Terence Chain reported multiple Nimbus blocks with invalid PTC (Payload Transaction Certificate) signatures across different versions (v26.3.0-12730e and v26.3.0-1077b0), providing a detailed table of 12 affected blocks with their slots, proposers, and timestamps. The issue appeared to affect multiple Nimbus validators over several hours.

Tomi0x investigated and identified the root cause: Nimbus had not properly upgraded their `is_invalid_indexed_payload_attestation` function, which was still using version `1.6.0-alpha.6`. This caused the function to compute the signing domain against `GENESIS_EPOCH` instead of the actual epoch of the `PayloadAttestationData`, resulting in invalid signatures. The bug specifically manifested in scenarios where Gloas (likely a protocol upgrade) wasn't activated at Genesis, a case that appears to be missing from the current spec test coverage.

**Participants:** agnxsh, potuz, terencechain, tomi0x

<details>
<summary>Raw messages</summary>

```
[2026-03-18T03:44:15.120000+00:00] terencechain: Side note, i saw some <@&595681804422479873> blocks with invalid PTC signatures. 
```
  │  #  │ Time (UTC) │ Slot  │ Proposer │ Nimbus Version │  Block Root   │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 1   │ 19:43:35   │ 95158 │ 1850     │ v26.3.0-12730e │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 2   │ 19:44:35   │ 95163 │ 1895     │ v26.3.0-12730e │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 3   │ 19:54:23   │ 95212 │ 1888     │ v26.3.0-12730e │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 4   │ 20:15:11   │ 95316 │ 1876     │ v26.3.0-12730e │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 5   │ 20:56:35   │ 95523 │ 1878     │ v26.3.0-1077b0 │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 6   │ 21:02:11   │ 95551 │ 1892     │ v26.3.0-1077b0 │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 7   │ 21:02:59   │ 95555 │ 1875     │ v26.3.0-1077b0 │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 8   │ 21:26:35   │ 95673 │ 1898     │ v26.3.0-1077b0 │ —             │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 9   │ 21:38:59   │ 95735 │ 1854     │ v26.3.0-1077b0 │ 0x83806ee9... │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 10  │ 22:00:47   │ 95844 │ 1851     │ v26.3.0-1077b0 │ 0x27c81f7c... │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 11  │ 22:04:11   │ 95861 │ 1867     │ v26.3.0-1077b0 │ 0x33bede8d... │
  ├─────┼────────────┼───────┼──────────┼────────────────┼───────────────┤
  │ 12  │ 22:40:59   │ 96045 │ 1864     │ v26.3.0-1077b0 │ 0x67ee1d5b... │
```
[2026-03-18T04:12:50.785000+00:00] agnxsh: cc <@822741226088169479> <@1305213827398500432>
[2026-03-18T04:23:38.525000+00:00] potuz: Oh nice if we had a split already on PTC, anyway asleep now
[2026-03-18T08:32:01.646000+00:00] tomi0x: Taking a look at it
[2026-03-18T10:21:24.974000+00:00] tomi0x: thanks, found it. we had not upgraded our `is_invalid_indexed_payload_attestation`  function (was still on version `1.6.0-alpha.6`) so we were computing the signing domain against `GENESIS_EPOCH` instead of the actual epoch of the `PayloadAttestationData`
[2026-03-18T10:30:15.445000+00:00] tomi0x: spec tests doesn't seem to cover this case, where Gloas isn't activated at Genesis
```

</details>

