# Accessing Prysm Node Logs

**Channel:** epbs | **Date:** 2026-03-04

## Summary

In this brief technical discussion, potuz asked barnabasbusa how to access logs from a second Prysm beacon node. Potuz explained that using `docker logs beacon` only showed logs from one node, and they specifically wanted to access logs from "prysm-geth-2" (the second node).

Barnabasbusa suggested using SSH to connect directly to the second Prysm node to view its logs, similar to how potuz had accessed the first node. After initially being unclear about the SSH connection details, potuz indicated they found the relevant information in a previous message from barnabasbusa, resolving the issue.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-04T16:06:41.294000+00:00] potuz: <@412614104222531604> how do I see the second beacon logs from Prysm?
[2026-03-04T16:07:02.281000+00:00] barnabasbusa: what do you mean second beacon logs?
[2026-03-04T16:07:10.552000+00:00] barnabasbusa: you can ssh into any prysm nodes
[2026-03-04T16:07:16.050000+00:00] potuz: If I do docker logs beacon I get one node
[2026-03-04T16:07:23.684000+00:00] potuz: how do I get to the second one prysm-geth-2
[2026-03-04T16:07:25.477000+00:00] barnabasbusa: ssh into the second node
[2026-03-04T16:07:26.218000+00:00] barnabasbusa: 😄
[2026-03-04T16:07:32.686000+00:00] potuz: where are those lol
[2026-03-04T16:07:41.364000+00:00] barnabasbusa: what do you mean? How did you get to the first one?
[2026-03-04T16:07:54.850000+00:00] potuz: ahh found your message
[2026-03-04T16:08:26.474000+00:00] barnabasbusa: all good?
```

</details>
