# Free DA issues with requests in beacon blocks

---

## 2026-04-10 (epbs)

tbenr raised concerns about potential "free DA (data availability) issues" with a new design that includes requests written onchain in beacon blocks, compared to the current design where requests are not written in beacon blocks. The concern centered on whether this change could be exploited since requests don't affect payload validity by design.

potuz clarified that having requests in beacon blocks wouldn't create new free DA issues compared to the current system, drawing a parallel to how builders can already include whatever they want today as long as the payload remains valid. The discussion concluded with both participants agreeing that this represents no significant change from the current behavior regarding payload validity requirements.

**Participants:** potuz, tbenr

<details>
<summary>Raw messages</summary>

```
[2026-04-10T14:59:47.084000+00:00] tbenr: speaking of which.

current design doesn't have requests written onchain in beacon blocks, while the new design has.
If requests are not affecting payload validity by design, is the new design open to big free DA issues?
[2026-04-10T15:01:57.608000+00:00] potuz: Having them in the bid would give free DA but in the block how so?
[2026-04-10T15:01:58.450000+00:00] tbenr: oh this is similar to what we have today
[2026-04-10T15:02:25.387000+00:00] potuz: Today the builder can include whatever it wants
[2026-04-10T15:02:27.568000+00:00] potuz: Anyway
[2026-04-10T15:02:34.488000+00:00] tbenr: no no, talking about unblinded requests in beaconblock
[2026-04-10T15:02:55.467000+00:00] potuz: As long as the Payload is valid I don't see any change from today
[2026-04-10T15:03:05.891000+00:00] tbenr: ye exactly
```

</details>

