# Lighthouse EPBS Flag Configuration and Cold DB Migration

**Channel:** epbs | **Date:** 2026-02-27

## Summary

0xunclebill shared that users running the Lighthouse EPBS branch need to include the `--epochs-per-migration 99999` flag to prevent issues with cold database migration. This flag is necessary because Lighthouse's cold database hasn't been updated yet to handle the new post-block vs post-payload envelope states introduced in EPBS, so the flag prevents the client from attempting to migrate data to the cold database.

The workaround will cause increased disk space usage since data isn't being migrated to cold storage, but 0xunclebill noted this should be negligible for short-lived development networks. Barnabasbusa added this information to the notes page to ensure other developers are aware of this requirement when testing the EPBS implementation.

## Participants

- 0xunclebill
- barnabasbusa

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-02-27T02:29:02.983000+00:00] 0xunclebill: just an fyi if you're trying to run the lighthouse epbs branch you'll also need to include this flag `--epochs-per-migration 99999`
[2026-02-27T08:15:38.302000+00:00] barnabasbusa: can you explain what is this flag doing? It sounds like a hack for now?
[2026-02-27T08:19:35.516000+00:00] 0xunclebill: We haven’t updated our cold db to handle post block vs post payload envelope states yet. This flag prevents LH from migrating data to the cold db.
[2026-02-27T08:20:18.353000+00:00] 0xunclebill: you might see an uptick in disk space usage because of this. but this should be negligible for short lived devnets
[2026-02-27T08:24:49.521000+00:00] barnabasbusa: added it to to the notes page so ppl are aware of this
```

</details>
