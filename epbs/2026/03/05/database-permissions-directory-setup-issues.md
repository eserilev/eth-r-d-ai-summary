# Database permissions and directory setup issues

**Channel:** epbs | **Date:** 2026-03-05

## Summary

Potuz encountered a permission denied error when trying to start a beacon node, as the system couldn't create the database directory at `/data/beaconchaindata`. The error occurred because potuz had wiped the entire `/data/prysm` directory instead of just its contents, which removed the directory's proper ownership permissions along with the data.

Barnabasbusa provided the solution: stop the beacon node, run `sudo chown prysm:prysm /data/prysm` to restore proper ownership permissions, and restart the beacon service. The issue was resolved by fixing the directory permissions that were lost when the entire directory was deleted and recreated, rather than just clearing its contents.

At the end of the discussion, potuz confirmed the beacon node was running with Prysm version v7.1.3-rc.3, though they noted it was still on an older commit, suggesting there may be an outstanding update to perform.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T01:07:51.868000+00:00] potuz: [2026-03-05 01:07:30.07] FATAL main: unable to start beacon node: could not open database: could not create database at /data/beaconchaindata: mkdir /data/beaconchaindata: permission denied[2026-03-05 01:07:30.07] FATAL main: unable to start beacon node: could not open database: could not create database at /data/beaconchaindata: mkdir /data/beaconchaindata: permission denied
[2026-03-05T01:08:07.190000+00:00] barnabasbusa: lol
[2026-03-05T01:08:13.294000+00:00] potuz: I wiped /data/prysm
[2026-03-05T01:08:18.789000+00:00] potuz: and restarted the beacon docker
[2026-03-05T01:08:22.948000+00:00] barnabasbusa: told you to delete everything inside that dir
[2026-03-05T01:08:27.413000+00:00] barnabasbusa: not including prysm dir
[2026-03-05T01:08:32.935000+00:00] potuz: it's there
[2026-03-05T01:08:38.303000+00:00] barnabasbusa: stop beacon
[2026-03-05T01:08:50.770000+00:00] barnabasbusa: sudo chown prysm:prysm /data/prysm
[2026-03-05T01:08:55.192000+00:00] barnabasbusa: and restart beacon
[2026-03-05T01:09:06.590000+00:00] barnabasbusa: yeah because it recreated
[2026-03-05T01:09:15.042000+00:00] barnabasbusa: you nuked the whole dir and you nuked its perms too
[2026-03-05T01:09:22.710000+00:00] barnabasbusa: instead of nuking the content of the dir
[2026-03-05T01:09:58.840000+00:00] potuz: yeah cause I didn't want to deal with permissions of not mathcing anything on /*
[2026-03-05T01:10:22.878000+00:00] barnabasbusa: sudo doesn’t care about permissions lol
[2026-03-05T01:12:18.978000+00:00] potuz: yes with /*
[2026-03-05T01:12:32.871000+00:00] potuz: Prysm/v7.1.3-rc.3/c32441537f1d2f745f275fbf605709aef1aa5827.
[2026-03-05T01:14:07.338000+00:00] potuz: it's still the old commit
```

</details>
