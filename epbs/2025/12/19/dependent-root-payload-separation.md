# Dependent root and payload separation

**Channel:** epbs | **Date:** 2025-12-19

## Summary

dapplion raised a technical concern about how payload separation affects the `dependent_root` mechanism in Ethereum's consensus layer. They noted that this interaction could potentially impact shuffling stability when a dependent root is used, which would be a significant issue for validator operations.

To address this concern, dapplion has written a technical document analyzing the problem and is seeking review from other researchers and developers. They suggest that the current specification may need to be modified to ensure shufflings remain stable in the presence of dependent roots when payload separation is implemented. The discussion appears to be in its early stages, with dapplion looking for feedback on their analysis and proposed solutions.

## Participants

- dapplion

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2025-12-19T23:41:11.149000+00:00] dapplion: Has someone thought about how payload separation affects the `depedant_root`? I have writen a doc about the topic, would appreciate some eyes on it as we may have to change the spec to keep the shufflings stable given a dependant_root

https://hackmd.io/@dapplion/gloas_dependant_root
```

</details>
