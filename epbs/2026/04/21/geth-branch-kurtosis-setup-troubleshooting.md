# Geth branch and Kurtosis setup troubleshooting

**Channel:** epbs | **Date:** 2026-04-21

## Summary

Stefan Bratanov asked about which Geth branch to use for testing new engine API methods. Barnabasbusa recommended using the `ethpandaops/geth:glamsterdam-devnet-0` branch and provided a link to the corresponding devnet documentation.

Stefan then encountered a Starlark evaluation error in Kurtosis related to an undefined `GpuConfig` reference in the zkboost launcher. Barnabasbusa identified this as likely a version issue and asked if Stefan was running Kurtosis 1.18.1, then referenced a previous Discord message with the solution. Stefan confirmed he had missed the earlier message and was able to resolve the issue after reviewing it.

The discussion was resolved with Stefan getting both the correct Geth branch information and a fix for his Kurtosis setup problem.

## Participants

- barnabasbusa
- stefanbratanov

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-04-21T13:43:45.325000+00:00] stefanbratanov: <@412614104222531604> which geth branch I can use which is using the new engine api methods?
[2026-04-21T13:43:56.227000+00:00] barnabasbusa: `ethpandaops/geth:glamsterdam-devnet-0`
[2026-04-21T13:44:36.132000+00:00] barnabasbusa: https://notes.ethereum.org/@ethpandaops/glamsterdam-devnet-0
[2026-04-21T15:02:01.777000+00:00] stefanbratanov: I am getting this weird error on Kurtosis, any idea <@412614104222531604> ?

```
INFO[2026-04-21T16:01:00+01:00] Enclave 'test-epbs' created successfully
There was an error interpreting Starlark code
Evaluation error: Multiple errors caught interpreting the Starlark script. Listing each of them below.
        at [github.com/ethpandaops/ethereum-package/src/zkboost/zkboost_launcher.star:272:17]: undefined: GpuConfig (did you mean get_config?)
        at [github.com/ethpandaops/ethereum-package/main.star:67:24]: <toplevel>

Error encountered running Starlark code.
```
[2026-04-21T15:02:24.096000+00:00] barnabasbusa: are you running 1.18.1?
[2026-04-21T15:02:44.760000+00:00] barnabasbusa: https://discord.com/channels/595666850260713488/892088344438255616/1493984222422237247 pls see this message
[2026-04-21T15:04:22.242000+00:00] stefanbratanov: got it, missed that message, all good now, thanks
```

</details>
