# Watchtower and image update debugging

**Channel:** epbs | **Date:** 2026-03-05

## Summary

The discussion centers around debugging a watchtower system that's experiencing Docker image update issues. Potuz reported concerning logs showing digest retrieval failures and fallback to full Docker pulls due to DNS/network connectivity problems with Docker Hub. The root cause was identified by barnabasbusa as someone (Pari) deploying a specific commit hash instead of using the global tag, which prevented automatic updates.

Barnabasbusa provided a manual workaround involving using the `runlike` tool to extract the current container configuration, stopping and removing the existing beacon container, pulling the updated image without the commit hash (`ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync`), and restarting with the corrected image tag. After some troubleshooting with typos in the image name and clarification on removing the commit hash from the Docker run command, potuz successfully got the container running in foreground mode.

The immediate issue appears resolved with the container starting up, though potuz is monitoring whether it reaches head status. An implicit action item is mentioned regarding addressing this deployment issue with Pari in the morning to prevent future occurrences of manually specifying commit hashes instead of using the intended global tags.

## Participants

- barnabasbusa
- potuz

## Raw Messages

<details>
<summary>Show raw messages</summary>

```
[2026-03-05T01:41:21.584000+00:00] potuz: Are you sure the watchtower works? all logs are scary like this
```time="2026-03-05T01:38:57Z" level=warning msg="Digest retrieval failed, falling back to full pull" container=beacon error="failed to get token: failed to execute challenge request: failed to execute HTTP request: Get \"https://index.docker.io/v2/\": dial tcp: lookup index.docker.io on 185.12.64.2:53: server misbehaving" image="ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync-c324415"```
[2026-03-05T01:45:04.759000+00:00] potuz: the hash at least is still pointing to the old commit
[2026-03-05T01:47:35.012000+00:00] barnabasbusa: wtf
[2026-03-05T01:47:58.671000+00:00] barnabasbusa: is dockerhub down?
[2026-03-05T01:53:01.943000+00:00] barnabasbusa: ah ffs
[2026-03-05T01:53:07.756000+00:00] barnabasbusa: pari rolled out a specific commit
[2026-03-05T01:53:19.550000+00:00] barnabasbusa: instead of the global tag
[2026-03-05T01:53:27.779000+00:00] potuz: so what do I do now
[2026-03-05T01:53:54.493000+00:00] potuz: need to sleep ser please, wanna see this finalizing and analyze the attestations reorgs
[2026-03-05T01:54:18.225000+00:00] barnabasbusa: docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
    assaflavie/runlike beacon
[2026-03-05T01:54:21.857000+00:00] barnabasbusa: run this
[2026-03-05T01:54:36.173000+00:00] barnabasbusa: then docker stop beacon && docker rm beacon
[2026-03-05T01:55:16.703000+00:00] barnabasbusa: then docker pull ethpandaops/prsym-beacon-chain:epbs-devnet-0-sync
[2026-03-05T01:55:43.630000+00:00] barnabasbusa: then run the output of the runlike command but replace the docker image with the the tag without the hash
[2026-03-05T01:56:23.877000+00:00] barnabasbusa: https://tenor.com/view/bonk-gif-13392138837084579216
[2026-03-05T01:56:34.285000+00:00] barnabasbusa: i’ll do this to pari in the morning
[2026-03-05T01:56:36.989000+00:00] barnabasbusa: lol
[2026-03-05T01:57:17.328000+00:00] potuz: docker pull ethpandaops/prsym-beacon-chain:epbs-devnet-0-sync
Error response from daemon: pull access denied for ethpandaops/prsym-beacon-chain, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
[2026-03-05T01:58:09.699000+00:00] barnabasbusa: ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync
[2026-03-05T01:58:16.787000+00:00] barnabasbusa: did i make a typo?
[2026-03-05T01:58:46.329000+00:00] potuz: this one worked  ~ docker pull ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync
epbs-devnet-0-sync: Pulling from ethpandaops/prysm-beacon-chain
01d7766a2e4a: Already exists
833317a9538e: Pull complete
ef31a3fee190: Pull complete
d2ba3d4268c1: Pull complete
098ae3f16034: Pull complete
939e9993f44c: Pull complete
Digest: sha256:8edce2e7f368abcece8f90cf8454bbc6c8f5167717e72025dc4624eca3fb704e
Status: Downloaded newer image for ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync
docker.io/ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync
[2026-03-05T01:58:51.320000+00:00] potuz: what now
[2026-03-05T01:59:14.009000+00:00] barnabasbusa: what’s the diff ??!
[2026-03-05T01:59:14.831000+00:00] barnabasbusa: lmao
[2026-03-05T01:59:40.910000+00:00] potuz: no clue, I'm in ctrl-c ctrl-v mode now
[2026-03-05T01:59:46.268000+00:00] potuz: what do I need to paste there 🙂
[2026-03-05T02:00:37.827000+00:00] barnabasbusa: did it work ?
[2026-03-05T02:00:51.178000+00:00] potuz: the command above is the last I typed
[2026-03-05T02:00:55.701000+00:00] potuz: what do I need to do now?
[2026-03-05T02:01:15.001000+00:00] potuz: start the container?
[2026-03-05T02:02:29.466000+00:00] potuz: ?
[2026-03-05T02:03:03.779000+00:00] potuz: well docker start beacon doesn't work so no clue
[2026-03-05T02:03:04.399000+00:00] barnabasbusa: yes just remove the commit hash
[2026-03-05T02:03:17.157000+00:00] barnabasbusa: you ran this right?
[2026-03-05T02:03:27.665000+00:00] potuz: I don't know what you are talking about and yes I did run that
[2026-03-05T02:03:33.622000+00:00] potuz: I do not know what removing the commit hash is
[2026-03-05T02:03:42.243000+00:00] barnabasbusa: what was the output of that?
[2026-03-05T02:04:21.447000+00:00] potuz: \
[2026-03-05T02:05:48.475000+00:00] potuz: I'll try that command without the hash
[2026-03-05T02:06:00.043000+00:00] barnabasbusa: docker run --name=beacon --hostname=f483c44f7fd0 --user=1007 --entrypoint /app/cmd/beacon-chain/beacon-chain --volume /data/prysm:/data --volume /data/execution-auth.secret:/execution-auth.jwt:ro --volume /data/ethereum-network-config/metadata:/network-config:ro --env=VIRTUAL_HOST=bn-prysm-geth-1.srv.epbs-devnet-0.ethpandaops.io --env=VIRTUAL_PORT=5052 --env=LETSENCRYPT_HOST=bn-prysm-geth-1.srv.epbs-devnet-0.ethpandaops.io --network=shared --workdir=/app/cmd/beacon-chain/beacon-chain.runfiles/prysm -p 127.0.0.1:5052:5052 -p 127.0.0.1:5054:5054 -p 127.0.0.1:6061:6061 -p 9000:9000 -p 9000:9000/udp --restart=always --log-opt max-file=8 --log-opt max-size=500m --runtime=runc -t ethpandaops/prysm-beacon-chain:epbs-devnet-0-sync --accept-terms-of-use=true --datadir=/data --p2p-host-ip=178.104.5.127 --p2p-tcp-port=9000 --p2p-udp-port=9000 --rpc-host=0.0.0.0 --rpc-port=4000 --jwt-secret=/execution-auth.jwt --execution-endpoint=http://snooper-engine:8561 --http-host=0.0.0.0 --http-port=5052 --monitoring-host=0.0.0.0 --monitoring-port=5054 --subscribe-all-data-subnets --pprof --pprofport=6061 --pprofaddr=0.0.0.0 '--grpc-gateway-corsdomain=*' --chain-config-file=/network-config/config.yaml --genesis-state=/network-config/genesis.ssz --contract-deployment-block=0 --min-sync-peers=1 --verbosity=debug --subscribe-all-subnets --bootstrap-node=enr:-LG4QB41B3b0O6_dn8oeyy63zrLh1x4L5OZVQJj-0tTI6f2_cPnzXr0-hPRnhl1Ao1BGNmOHs5McgWRFGGf8fAkM8MECg2V0aMvKhG7DqheEaahJ34RldGgykFzbiRb_______________-CaWSCdjSCaXCEW2Lp5YlzZWNwMjU2azGhAkXc0la9sb22bqYDtmOPgUQmHO8kkC4sxRdB8amJyY0Qg3RjcIIjMoN1ZHCCIzI
[2026-03-05T02:06:10.045000+00:00] barnabasbusa: yes
[2026-03-05T02:06:37.247000+00:00] barnabasbusa: you run this making sure that the image you set is without the commit hash
[2026-03-05T02:06:51.423000+00:00] potuz: This is running but in the foreground now?
[2026-03-05T02:07:13.702000+00:00] barnabasbusa: yeah
[2026-03-05T02:07:17.824000+00:00] barnabasbusa: can do -d
[2026-03-05T02:07:19.333000+00:00] potuz: 🙁
[2026-03-05T02:07:23.833000+00:00] barnabasbusa: if you wanna send it back
[2026-03-05T02:07:34.330000+00:00] potuz: will see if it gets to head
[2026-03-05T02:07:36.692000+00:00] potuz: first
[2026-03-05T02:15:03.044000+00:00] barnabasbusa: alright
[2026-03-05T02:15:09.593000+00:00] barnabasbusa: good luck
```

</details>
