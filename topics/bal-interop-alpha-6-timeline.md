# BAL interop and alpha 6 timeline

---

## 2026-04-15 (epbs)

The team confirmed that BAL (Blob Attribute Lists) changes will not be included in the current release but will instead be targeted for the upcoming alpha.6 release. This decision aligns with the interoperability testing timeline, as they plan to use alpha.6 with BAL functionality during the interop rather than attempting to include BAL changes in the immediate release.

The alpha.6 release is scheduled for approximately one week from the discussion date (around April 22, 2026), and will include the necessary changes to support BALs. This approach allows for a more controlled rollout of the BAL functionality while ensuring it's available for the planned interoperability testing phase.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-15T17:01:01.657000+00:00] terencechain: we are not targeting bal before interop right? if we are, then we need the bal change in the release, but probably better for them to land in alpha.6 and target that during interop
[2026-04-15T17:02:17.744000+00:00] jtraglia: Yes, not in this release. Planning to do a fairly quick alpha.6 release with the changes necessary for BALs 👍
[2026-04-15T17:02:46.382000+00:00] jtraglia: Targeting alpha.6 about this time next week.
```

</details>

---

## 2026-04-19 (epbs)

The Ethereum R&D team successfully released consensus-specs v1.7.0-alpha.5 on April 19th, 2026. The release process was initiated by jtraglia and completed later that day, with the final release available on GitHub.

Initial testing results show positive compatibility across major Ethereum clients. Terencechain confirmed that Prysm passes all alpha.5 spec tests for both mainnet and minimal configurations, while jtraglia noted that Lighthouse has also achieved full test compatibility with the new release.

**Participants:** jtraglia, terencechain

<details>
<summary>Raw messages</summary>

```
[2026-04-19T13:00:18.039000+00:00] jtraglia: Kicked off the alpha.5 release action. Should finish <t:1776621600:R>.
https://github.com/ethereum/consensus-specs/actions/runs/24629367848
[2026-04-19T17:06:22.131000+00:00] terencechain: Release · ethereum/consensus-specs@a6687...
[2026-04-19T18:00:29.262000+00:00] terencechain: nice, looks like it's there: https://github.com/ethereum/consensus-specs/releases/tag/v1.7.0-alpha.5
[2026-04-19T22:20:43.792000+00:00] terencechain: can confirm prysm passes alpha.5 mainnet and minimal spec tests
[2026-04-19T22:21:48.772000+00:00] jtraglia: Awesome. I saw that lighthouse was also able to get everything passing. Great to hear.
[2026-04-19T22:21:50.261000+00:00] jtraglia: 
```

</details>

---

## 2026-04-24 (epbs)

A proposal has been made to create a new alpha.6 release for the Ethereum consensus specs that would include several new and fixed tests along with PR #5152, which contains a small consensus-breaking change. The [full list of changes](https://github.com/ethereum/consensus-specs/compare/v1.7.0-alpha.5...master) since alpha.5 shows the scope of updates that would be included.

However, there is uncertainty about the timing of this release. The team agrees they should wait until the genesis handling issue (#5149) is resolved before proceeding. Additionally, there are concerns about potential issues that might arise at the beginning of interop testing, which could necessitate another release. The discussion concludes with the acknowledgment that they may need to make multiple releases during the week, suggesting flexibility in their release strategy depending on how technical issues unfold.

**Participants:** jtraglia

<details>
<summary>Raw messages</summary>

```
[2026-04-24T21:49:03.098000+00:00] jtraglia: <@586161934425128960> suggested maybe making a new alpha.6 release which contains several new/fixed tests and [5152](https://github.com/ethereum/consensus-specs/pull/5152) which is a (small) consensus breaking change. [Here](https://github.com/ethereum/consensus-specs/compare/v1.7.0-alpha.5...master) is the full list of changes since the alpha.5 release. I think we should at least wait until [the genesis handling issue](https://github.com/ethereum/consensus-specs/issues/5149) is resolved (cc <@1019999229151821936>, <@602753420033785856>, <@504202741933932544>). Also, there's a chance we run into some issue which requires a release at the beginning of the interop. Not sure if we should wait a few more days or not. Worst case we make a few releases this week.
```

</details>

---

## 2026-04-25 (epbs)

This discussion contains only a brief reference to "alpha6" posted by barnabasbusa. Without additional context or follow-up messages, no technical details, decisions, or action items can be determined from this single message reference.

**Participants:** barnabasbusa

<details>
<summary>Raw messages</summary>

```
[2026-04-25T04:10:28.004000+00:00] barnabasbusa: alpha6
```

</details>

