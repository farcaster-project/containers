# `monerod` container

Build the image against `ubuntu:20.04` with

```
docker build --build-arg VRS=v0.17.2.3 -t monerod:latest .
```

Available `build-arg`:

- **VRS**: monerod version to install, default _0.17.2.3_

Create a container with

```
docker create -p 18081:18081\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    monerod:latest
```

Available environment variables:

- **NETWORK**: a flag intended for the network, but this can be used more broadly as it is directly passed to `monerod` with a prepended `--`
- **MONEROD_RPC_PORT**: the RPC port to use
- **OFFLINE**: a flag intended for usage with a regtest network. Needs to have `--` preprended, e.g. `--offline`
- **DIFFICULTY**: a flag intended for usage with a regtest network. Sets a fixed difficulty for generating blocks. For non-regtest set this to `0`, for regtest `1` is the recommended value.

RPC is binded to `0.0.0.0` and accepts connections from everywhere.

All the ports are exposed by defaut. Other monero binaries, like the `monero-wallet-cli`, are also installed.

## GitHub Action usage

```yaml
services:
  monerod:
    image: ghcr.io/farcaster-project/containers/monerod
    env:
      NETWORK: regtest
      MONEROD_RPC_PORT: 18081
      OFFLINE: --offline
      DIFFICULTY: 1
    ports:
      - 18081:18081
```

## Standalone usage

```
ID=$(docker create -p 18081:18081\
    --name monerod\
    --env NETWORK=regtest\
    --env MONEROD_RPC_PORT=18081\
    --env OFFLINE=--offline\
    --env DIFFICULTY=1\
    ghcr.io/farcaster-project/containers/monerod:latest)

docker start $ID
...
docker kill $ID
docker container rm $ID
```
